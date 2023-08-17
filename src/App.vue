<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
   
    <SiteNavigation/>
   <router-view/>
  </div>
</template>

<script setup>
import SiteNavigation from './components/SiteNavigation.vue';
</script>

