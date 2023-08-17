<template>

        <div class="flex  px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer">
            <div class="flex flex-col flex-1">
                <h2 class="text-3xl">{{ city.city }}</h2>
                <h3>{{ city.state }}</h3>
            </div>
            <div class="flex flex-col gap-2" v-if="city.weather">
                <!-- this snippet {{ city.weather && city.weather.main && city.weather.main.temp
                ?  and the above v-if are doing the same function waiting for the data from citylist before rendering on the screen im just going to leave it. -->
                <P class="text-3xl self-end"> {{ city.weather && city.weather.main && city.weather.main.temp
                ? (Math.round(city.weather.main.temp))
                : '' }}&deg;</P>
               <div class="flex gap-2 md:text-xs text-lg">
                <span class=" ">
                    high:
                        {{ Math.round(city.weather.main.temp_max )}}&deg;
                </span>
                <span class=" ">
                    low:
                        {{ Math.round(city.weather.main.temp_min )}}&deg;
                </span>
               </div>
               
            </div>
        </div>

</template>

<script setup>

defineProps({
    city: {
        type:Object,
        default: () => {},
    }
})



</script>

