<template>
    <div>
          <div class="flex flex-col flex-1 items-center ">
            <!-- banner -->
            <div v-if="route.query.preview"  class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>You are currently previewing this city, click the "+" icon to start tracking this city.</p>
            </div>
            <div v-if="weatherData">
          <!-- weather overview -->
          <div class="flex flex-col items-center text-white py-12" v-if="weatherData">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
             <p class="text-sm mb-12">
        {{
          new Date(weatherData.currentTime).toLocaleDateString(
            "en-us",
            {
              weekday: "short",
              day: "2-digit",
              month: "long",
            }
          )
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString(
            "en-us",
            {
              timeStyle: "short",
            }
          )
        }}
      </p>
      <P class="text-8xl mb-8">{{ Math.round(weatherData.current.temp) }}&deg;</P>
      
        <P>Feels like  {{ Math.round(weatherData.current.feels_like) }}&deg;</P>
        <P class="capitlaize">{{ weatherData.current.weather[0].description }}</P>
        <img class="w-[150px] h-auto" :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`" />
   
          </div>

          <hr class=" border-white border-opacity-10 border w-full"/>

          <!-- hourly weather -->
          <div class="max-w-[400px] md:max-w-screen-md w-full py-12 ">
            <div class="mx-8 text-white">
                <h2 class=" mb-4 flex justify-center text-2xl underline">Hourly Weather Forecast</h2>
                <div class="flex gap-10 overflow-x-scroll"> 
                    <div
            v-for="hourData in weatherData.hourly"
            :key="hourData.dt"
            class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap md:text-md  text-sm">
              {{
                new Date(
                  hourData.currentTime
                ).toLocaleTimeString("en-us", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              class="w-auto h-[50px] object-cover"
              :src="
                `http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`
              "
              alt=""
            />
            <p class="text-xl">
              {{ Math.round(hourData.temp) }}&deg;
            </p>
          </div>
                </div>
            </div>
          </div>

          <hr class=" border-white border-opacity-10 border w-full"/>

          <!-- weekly weather -->
          <div class="max-w-screen-md w-full py-12 flex justify-center">
            <div class="mx-8 text-white">
                <h2 class="mb-4 flex justify-center text-2xl underline">Daily Weather Forecast</h2>
                <div v-for="day in weatherData.daily" :key="day.dt" class="flex items-center gap-6 md:gap-[170px]">
                    <p class="flex-1">
            {{
              new Date(day.dt * 1000).toLocaleDateString(
                "en-us",
                {
                  weekday: "long",
                }
              )
            }}
          </p>
          <img
            class="w-[50px] h-[50px] object-cover"
            :src="
              `http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`
            "
            alt=""
          />
          <div class="flex gap-2 flex-1 justify-end">
            <p>High: {{ Math.round(day.temp.max) }}&deg;</p>
            <p>Low: {{ Math.round(day.temp.min) }}&deg;</p>
          </div>
            
            </div>
            </div>
          </div>
          <div class="flex  gap-2 py-12 justify-center text-white cursor-pointer duration-150 hover:text-red-500" @click="removeCity">
            <i class="fa-solid fa-trash pt-1"></i>
            <P >Remove City</P>
          </div>
        </div>

           </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { useRoute,useRouter } from 'vue-router';
import { ref,onMounted } from 'vue';


const route = useRoute();
const weatherData = ref(null);
const getWeatherData =async () => {
    try{
        const response = await axios.get(`https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=metric`);
        
         // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = response.data.current.dt * 1000 + localOffset;
    response.data.currentTime =
      utc + 1000 * response.data.timezone_offset;

    // cal hourly weather offset
    response.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime =
        utc + 1000 * response.data.timezone_offset;

        
    });

      

        weatherData.value = response.data;
        console.log(weatherData);
            
    }catch(e){
        console.log(e);
    }
};

onMounted(() => {
    getWeatherData();
})

const router = useRouter();
const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem('savedCities'));
  const updatedCities = cities.filter((city) => city.id !== route.query.id);
  
  localStorage.setItem("savedCities",JSON.stringify(updatedCities));

  router.push({
    name: "home",
  });
};

</script>

