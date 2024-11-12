<template>
    <div v-if="loading" class="text-center">
      Loading cities...
    </div>
    
    <div v-else>
      <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)" />
      </div>
    
      <p v-if="savedCities.length === 0">
        No locations added. To start tracking a location, search in the field above.
      </p>
    </div>
  </template>
  
  <script setup>
  import axios from "axios";
  import { ref, onMounted } from "vue";
  import { useRouter } from "vue-router";
  import CityCard from "./CityCard.vue";
  
  const savedCities = ref([]);
  const loading = ref(true); // Loading state
  
  const getCities = async () => {
    if (localStorage.getItem("savedCities")) {
      savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
      
      const requests = savedCities.value.map((city) =>
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
        )
      );
  
      const weatherData = await Promise.all(requests);
  
      weatherData.forEach((value, index) => {
        savedCities.value[index].weather = value.data;
      });
    }
    loading.value = false; // Set loading to false after data is loaded
  };
  
  // Use onMounted to call getCities when the component is mounted
  onMounted(() => {
    getCities();
  });
  
  const router = useRouter();
  const goToCityView = (city) => {
    router.push({
      name: "cityView",
      params: { state: city.state, city: city.city },
      query: {
        id: city.id,
        lat: city.coords.lat,
        lng: city.coords.lng,
      },
    });
  };
  </script>
  