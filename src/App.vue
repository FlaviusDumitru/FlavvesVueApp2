<script setup>
import { RouterView } from 'vue-router';

import {ref, onMounted} from 'vue';
import router from './router';

const positions = ref([])
const valdCity = ref(null) // För att hålla koll på vald stad

onMounted(() => {
    positions.value = JSON.parse(localStorage.getItem('positions'))
    // Lägg den valda staden till den första staden i listan
})

// Metod att byta den valda staden
const bytCity = (city) => {
  router.push('/'+ city.location)  
}

</script>

<template>
  <h1>Flavves väderapp</h1>
  <p>Välj en stad (på jorden){{ valdCity }}</p>
  <!-- For loop med städerna -->
  <div class="containerButtons" v-for="city in positions" :key="city">
    <button @click ="bytCity(city)">{{ city.location }}</button>
  </div>
  <RouterView />
</template>

<style scoped>
h1,
p {
  text-align: center;
}

.containerButtons {
  text-align: center;
}

button {
  background-color: #8888885a; 
  color: #fff;
  border: 2px solid #888;
  padding: 0.7em; 
  border-radius: 0.2em;
  text-align: center; 
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s, color 0.3s; 
  margin: 0.1em;
}
</style>
