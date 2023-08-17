<template>
  <div>
     <main class=" container text-white">
      <div class="pt-4 mb-8 relative">
        <input type="text" v-model="searchQuery" @input="getSearchResults" 
        placeholder="search for city or state" 
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"/>
      <ul v-if="mapboxSearchResults" class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
        <P v-if="searchError">Sorry something went wrong,please try again</P>
        <P v-if="!serverError && mapboxSearchResults.length === 0">No results found, please try a different name</P>


        <template v-else>
          <li @click="previewCity(SearchResult)"
          v-for="SearchResult in mapboxSearchResults" :key="SearchResult.id"
          class="py-2 cursor-pointer">
          {{ SearchResult.place_name }}
          </li>
        </template>
      </ul>
      </div>
      <div class="flex flex-col gap-4">
        <suspense>
          <city-list/>
          <template #fallback>
            <P>loading..</P>
          </template>
        </suspense>
      </div>
     </main>
  </div>
</template>

<script setup>
 import CityList from '../components/CityList.vue';
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';


const router = useRouter();


const previewCity = (SearchResult) => {
  console.log(SearchResult);
  const [city, state] = SearchResult.place_name.split(',');
  router.push({
    name: 'CityView',
    params: {
        state: state.replaceAll(" ",""),
        city: city,
    },
    query: {
      lat: SearchResult.geometry.coordinates[1],
      lng: SearchResult.geometry.coordinates[0],
      preview: true,
    },
  })
 
}

 const mapboxAPIKey = "pk.eyJ1IjoidHJldm9yNTMiLCJhIjoiY2xrdHZ0cHpsMDEyYTNjcHFpcjNlZHpwMiJ9.ejOOxr9546vr_eZ9rMWpCg";
 const searchQuery = ref("");
 const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const searchError = ref(null);
 const getSearchResults =  () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout( async () => {
    if (searchQuery.value !== ''){
      try{ const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
      mapboxSearchResults.value = result.data.features;
      console.log(mapboxSearchResults.value);
    } catch {
      searchError.value = true;
    }
      
      return;
    }
    mapboxSearchResults.value = null;
  },300);
 }
</script>

