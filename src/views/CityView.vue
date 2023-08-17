<template>
    <div>
        <Suspense>
            
    <AsyncCityView/>
         
    <template #fallback>
        <P>loading</P>
    </template>
       </Suspense>
    </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue';

</script>

