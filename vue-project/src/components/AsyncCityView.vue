<template>
    <div>
      <h2>{{ city.city }}, {{ city.state }}</h2>
      <p v-if="city.weather">{{ city.weather?.main?.temp }}&deg; F</p>
      <p v-if="city.weather">High: {{ city.weather?.main?.temp_max }}&deg; F</p>
      <p v-if="city.weather">Low: {{ city.weather?.main?.temp_min }}&deg; F</p>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import axios from 'axios';
  
  const city = ref({
    city: 'Example City',
    state: 'Example State',
    coords: {
      lat: 40.771473, // Provide default latitude
      lng: -84.61543, // Provide default longitude
    },
  });
  
  const apiKey = '7efa332cf48aeb9d2d391a51027f1a71';
  
  // Function to fetch weather data
  const fetchWeatherData = async () => {
    if (city.value.coords.lat && city.value.coords.lng) {
      try {
        const response = await axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${city.value.coords.lat}&lon=${city.value.coords.lng}&appid=${apiKey}&units=imperial`
        );
        city.value.weather = response.data;
      } catch (error) {
        console.error('Error fetching weather data:', error);
      }
    } else {
      console.error('Latitude and longitude are undefined');
    }
  };
  
  // Fetch the weather data when the component is mounted
  onMounted(() => {
    fetchWeatherData();
  });
  </script>
  