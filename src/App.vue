<template>
  <div>
    <div class="search-bar mx-5">
      <b-form-input v-model="searchTerm" @input="searchCountries" placeholder="Search by Country Name"></b-form-input>
    </div>
    <table class="table mx-5">
      <thead>
        <tr>
          <th>Flags</th>
          <th @click="sortCountries('name')">Country Name</th>
          <th>Native Country Name</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="country in displayedCountries" :key="country.cca2">
          <td><img :src="country.flags.png" alt="Flag" class="flag-image" /></td>
          <td @click="showModal(country)">{{ country.name.official }}</td>
          <td>{{ country.name.nativeName.ara }}</td>
        </tr>
      </tbody>
    </table>
    <b-pagination v-model="currentPage" :total-rows="filteredCountries.length" :per-page="perPage" align="center"></b-pagination>

   <b-modal v-if="selectedCountry" v-model="selectedCountryModal" title="Country Details" class="country-modal">
    <div class="modal-content">
      <div class="flag-container" align="center">
        <img :src="selectedCountry.flags.png" alt="Flag" class="flag-image-modal" />
      </div>
      <br>
      <div class="country-details mx-5">
        <p><strong>Country Name:</strong> {{ selectedCountry.name.official }}</p>
        <p><strong>2 character Country Code:</strong> {{ selectedCountry.cca2 }}</p>
        <p><strong>3 character Country Code:</strong> {{ selectedCountry.cca3 }}</p>
        <p><strong>Native Country Name:</strong> {{ selectedCountry.name.nativeName.ara }}</p>
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

<style>
.flag-image {
  max-width: 30px;
  max-height: 20px;
}

.flag-image-modal {
  max-width: 50px;
  max-height: 30px;
}

.search-bar {
  margin-bottom: 10px;
}

</style>
