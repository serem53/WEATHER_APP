<template>
    <div class="md:max-w-screen-sm w-full">
    
        
        <div v-for="city in savedCities" :key="city.id">
        <city-card :city="city"  @click="goToCityView(city)" class=" hover:-translate-y-3  duration-200 " />
        </div>
     
      <p v-if="savedCities.length === 0">No locations added. To start tracking a location,search in the filed above.</p>
     
      
    </div>
</template>

<script setup>
import CityCard from './CityCard.vue';
import { ref,onMounted } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';



const savedCities = ref([]);

 const getCities = async () => {
     if(localStorage.getItem('savedCities')) {
         savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
         console.log(savedCities);

      // Check if savedCities has valid lat and lng values
    if (savedCities.value.every(city => city.coords && city.coords.lat && city.coords.lng)) {
      const requests = [];
      savedCities.value.forEach((city) => {
        const apiUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=metric`;
        requests.push(axios.get(apiUrl));
      });
      const weatherData = await Promise.all(requests);
      

      console.log(weatherData);

      weatherData.forEach((value, index) => {
        savedCities.value[index].weather = value.data;

       
      });
    }
  }
};

onMounted(async () => {
  await getCities();
});


const router = useRouter();
const goToCityView = (city) => {
  router.push({
    name: "CityView",
    params: { state: city.state, city: city.city },
    query: { 
      id: city.id,
      lat: city.coords.lat,
      lng: city.coords.lng,
    },
  });
};

</script>

