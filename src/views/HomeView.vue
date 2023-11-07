<template>
    <div>
      <p>Staden som visas nu är: </p>
        {{ props.location ?? 'ingen location' }}
      <WeatherCard v-for="day in weatherInfo" :key="day.date" :weather-data="day" />
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, watch } from 'vue';
  import WeatherCard from '../components/WeatherCard.vue';
  
  const props = defineProps(['location']);
  const weatherInfo = ref([]);
  const positions = ref([]);

  // Hämta väder-och platsinformation
  function hamtaData () {
    let position =
    positions.value.find((pos) => {
      return pos.location === props.location
    }) || positions.value[0]  
    let weatherAPI = `https://api.open-meteo.com/v1/forecast?latitude=${position.lat}&longitude=${position.long}&daily=weathercode,temperature_2m_max,temperature_2m_min,precipitation_sum,windspeed_10m_max,winddirection_10m_dominant&windspeed_unit=ms&timezone=auto`;
    fetch(weatherAPI)
      .then((response) => {
        if (response.status === 200) {
          return response.json();
        }
      })
      .then((data) => {
        console.log(data)
        if (data.length === 0) {
          throw 'Ingen data returnerades';
        }
        // For-loop som innehåller väderinfo
        weatherInfo.value = [];
        let items = data.daily.time.length;
        for (let index = 0; index < items; index++) {
          let wd = {};
          wd.date = data.daily.time[index];
          wd.weatherCondition = data.daily.weathercode[index];
          wd.maxTemp = data.daily.temperature_2m_max[index];
          wd.minTemp = data.daily.temperature_2m_min[index];
          wd.precipitation = data.daily.precipitation_sum[index];
          wd.windDirection = data.daily.winddirection_10m_dominant[index];
          wd.windSpeed = data.daily.windspeed_10m_max[index];
          weatherInfo.value.push(wd);
        }
      });
  }

  // Bevaka när staden har ändrats
  watch(props, () => {
    hamtaData();
})
// Hämta info från localstorage samt kör hamtaData funktionen.
  onMounted(() => {
    positions.value = JSON.parse(localStorage.getItem('positions'))
    hamtaData()})
  </script>
  
  <style scoped>
    WeatherCard {
      overflow: auto;
    }
  </style>