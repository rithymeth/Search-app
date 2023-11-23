<template>
  <div class="country-app">
    <div class="search-bar">
      <b-form-input v-model="searchTerm" @input="searchCountries" placeholder="Search by Country Name"></b-form-input>
    </div>

    <div class="country-list">
      <table class="table">
        <thead>
          <tr>
            <th>Flags</th>
            <th @click="sortCountries('name')">Country Name</th>
            <th>Native Country Name</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="country in displayedCountries" :key="country.cca2" @click="showModal(country)">
            <td><img :src="country.flags.png" alt="Flag" class="flag-image" /></td>
            <td>{{ country.name.official }}</td>
            <td>{{ country.name.nativeName.ara ? country.name.nativeName.ara.official : '...' }}</td>
          </tr>
        </tbody>
      </table>

      <b-pagination v-model="currentPage" :total-rows="filteredCountries.length" :per-page="perPage" align="center"></b-pagination>
    </div>

    <b-modal v-if="selectedCountry" v-model="selectedCountryModal" title="Country Details" class="country-modal" hide-header-close>
      <div class="modal-content">
        <div class="flag-container" align="center">
          <img :src="selectedCountry.flags.png" alt="Flag" class="flag-image-modal" />
        </div>
        <div class="country-details">
          <p><strong>Country Name:</strong> {{ selectedCountry.name.official }}</p>
          <p><strong>2 character Country Code:</strong> {{ selectedCountry.cca2 }}</p>
          <p><strong>3 character Country Code:</strong> {{ selectedCountry.cca3 }}</p>
          <p><strong>Native Country Name:</strong> {{ selectedCountry.name.nativeName.ara ? selectedCountry.name.nativeName.ara.official : '...' }}</p>
          <p><strong>Alternative Country Name:</strong> {{ selectedCountry.altSpellings.join(', ') }}</p>
          <p><strong>Country Calling Codes:</strong> {{ selectedCountry.idd.root + selectedCountry.idd.suffixes }}</p>
        </div>
      </div>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      countries: [],
      searchTerm: "",
      currentPage: 1,
      perPage: 25,
      selectedCountry: null,
      selectedCountryModal: false,
    };
  },
  computed: {
    filteredCountries() {
      return this.countries.filter(country =>
        country.name.official.toLowerCase().includes(this.searchTerm.toLowerCase())
      );
    },
    pageCount() {
      return Math.ceil(this.filteredCountries.length / this.perPage);
    },
    displayedCountries() {
      const start = (this.currentPage - 1) * this.perPage;
      const end = start + this.perPage;
      return this.filteredCountries.slice(start, end);
    },
  },
  methods: {
    async fetchCountries() {
      try {
        const response = await axios.get("https://restcountries.com/v3.1/all");
        this.countries = response.data;
      } catch (error) {
        console.error("Error fetching countries:", error);
      }
    },
    searchCountries() {
      this.currentPage = 1;
    },
    sortCountries(prop) {
      this.filteredCountries.sort((a, b) =>
        a[prop].localeCompare(b[prop])
      );
    },
    showModal(country) {
      this.selectedCountry = country;
      this.selectedCountryModal = true;
    },
    goToPage(page) {
      if (page >= 1 && page <= this.pageCount) {
        this.currentPage = page;
      }
    },
  },
  mounted() {
    this.fetchCountries();
  },
};
</script>

<style scoped>
.country-app {
  max-width: 800px;
  margin: auto;
  padding: 20px;
}

.search-bar {
  margin-bottom: 20px;
}

.country-list {
  margin-bottom: 20px;
}

.table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

th, td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #ddd;
  cursor: pointer;
}

th {
  background-color: #f2f2f2;
}

tr:hover {
  background-color: #f5f5f5;
}

.flag-image {
  max-width: 30px;
  max-height: 20px;
}

.flag-image-modal {
  max-width: 50px;
  max-height: 30px;
}

.country-modal {
  max-width: 400px;
}

.modal-content {
  padding: 20px;
}

.country-details {
  margin-top: 20px;
}

</style>
