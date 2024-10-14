<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input type="text" v-model="searchQuery" @input="getSearchResults" placeholder="Search for a city"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">

      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="searchResults || searchError">
        <p v-if="searchError"> Oops, something went wrong. Try again later. </p>
        <p v-if="!searchError && searchResults.length === 0"> No results matched your query, try a different term.</p>
        <li v-for="result in searchResults" :key="result.id" class="py-2 cursor-pointer">
          {{ result.name }}, {{ result.country }}
        </li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';

const searchQuery = ref('');
const queryTimeout = ref(null);
const searchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);  // Clear timeout to avoid multiple triggers
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const result = await axios.get(`https://geocoding-api.open-meteo.com/v1/search?name=${searchQuery.value}&format=json`);

        searchResults.value = result.data.results;  // Open-Meteo API uses 'results'
        console.log(searchResults.value.length);
      } catch (error) {
        searchError.value = true;
        searchResults.value = null; // Reset in case of an error
        console.error("Error fetching search results:", error);
      }
    }
    else {
      searchResults.value = null;
    }
  }, 300);
}
</script>