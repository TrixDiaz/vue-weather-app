<script setup>
import { ref, onMounted, computed } from 'vue';

const name = ('Weather App')
const cityName = ref('manila');
const temperature = ref(0);
const weatherDescription = ref('');
const weatherMain = ref('');
const humidity = ref(0);
const windSpeed = ref(0);
const error = ref('');

const fetchWeather = async (city) => {
  try {
    const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=20b6cb343e8e06368b38cce4d5938b96`);
    if (!response.ok) throw new Error('City not found');
    const data = await response.json();
    error.value = '';
    cityName.value = data.name;
    temperature.value = data.main.temp - 273.15; // Convert from Kelvin to Celsius
    weatherMain.value = data.weather[0].main.toLowerCase();
    weatherDescription.value = data.weather[0].description.toLowerCase();
    humidity.value = data.main.humidity;
    windSpeed.value = data.wind.speed;
  } catch (err) {
    error.value = err.message;
    console.log('May error ee', err);
  }
};

const handleSubmit = (event) => {
  event.preventDefault();
  fetchWeather(cityName.value);
};

onMounted(() => {
  fetchWeather(cityName.value);
});

const weatherIcon = computed(() => {
  switch (weatherMain.value) {
    case 'rain':
      return '🌧️';
    case 'snow':
      return '❄️';
    case 'mist':
      return '🌫️';
    case 'clear':
      return '☀️';
    case 'clouds':
      return '☁️';
    default:
      return '⛅';
  }
});
</script>

<template>
  <div class="flex flex-col items-center min-h-screen bg-gradient-to-r from-violet-500 to-fuchsia-500 pt-12">
    <header class="text-center mb-4 mt-10">
      <h1 class="text-4xl font-extrabold text-gray-100">{{ name }}</h1>
      <p class="text-gray-100 text-md max-w-sm">Wherever you go, no matter what the weather, always bring your own sunshine</p>
    </header>
    <div class="w-full max-w-lg mx-4 p-6 bg-white border border-gray-200 rounded-lg shadow-md transition-all ease-in-out duration-1000" :class="{'translate-y-4 opacity-0': !temperature}">
      <form @submit="handleSubmit" class="mb-2">
        <label for="search" class="mb-2 text-sm font-medium text-gray-900 sr-only dark:text-white">Search</label>
        <div class="relative">
          <div class="absolute inset-y-0 start-0 flex items-center ps-3 pointer-events-none">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="size-5 ml-2">
              <path fill-rule="evenodd"
                d="m9.69 18.933.003.001C9.89 19.02 10 19 10 19s.11.02.308-.066l.002-.001.006-.003.018-.008a5.741 5.741 0 0 0 .281-.14c.186-.096.446-.24.757-.433.62-.384 1.445-.966 2.274-1.765C15.302 14.988 17 12.493 17 9A7 7 0 1 0 3 9c0 3.492 1.698 5.988 3.355 7.584a13.731 13.731 0 0 0 2.273 1.765 11.842 11.842 0 0 0 .976.544l.062.029.018.008.006.003ZM10 11.25a2.25 2.25 0 1 0 0-4.5 2.25 2.25 0 0 0 0 4.5Z"
                clip-rule="evenodd" />
            </svg>
          </div>
          <input type="search" id="search" v-model="cityName"
            class="block w-full p-4 ps-10 text-sm text-gray-900 border border-gray-300 rounded-full bg-gray-50 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
            placeholder="Search" required />
          <button type="submit"
            class="text-white absolute end-2.5 bottom-2.5 bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-full text-sm px-4 py-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="size-5">
              <path fill-rule="evenodd"
                d="M9 3.5a5.5 5.5 0 1 0 0 11 5.5 5.5 0 0 0 0-11ZM2 9a7 7 0 1 1 12.452 4.391l3.328 3.329a.75.75 0 1 1-1.06 1.06l-3.329-3.328A7 7 0 0 1 2 9Z"
                clip-rule="evenodd" />
            </svg>
          </button>
        </div>
      </form>
      <div v-if="error" class="text-red-500 text-center mb-4">
        <p class="uppercase">❗ {{ error }}</p>
      </div>
      <div v-else class="flex flex-col justify-center items-center w-full gap-2 transition-all ease-in-out duration-1000" :class="{'translate-y-0 opacity-100': temperature, 'translate-y-4 opacity-0': !temperature}">
        <div class="flex flex-col justify-center items-center my-10">
          <p class="text-5xl my-1">{{ weatherIcon }}</p>
          <p class="text-2xl font-semibold">{{ temperature.toFixed(0) }}&#x2103;</p>
          <!-- Display temperature in Celsius -->
          <p class="capitalize">{{ weatherDescription }}</p>
        </div>
        <div class="flex flex-row justify-between items-center w-full gap-4">
          <div class="flex flex-row justify-between items-center gap-1">
            <div>
              <p class="text-4xl">🌊</p>
            </div>
            <div class="flex flex-col">
              <p>
                {{ humidity }}%
              </p>
              <p>Humidity</p>
            </div>
          </div>
          <div class="flex flex-row justify-between items-center gap-1">
            <div>
              <p class="text-4xl">🍃</p>
            </div>
            <div class="flex flex-col">
              <p>
                {{ windSpeed.toFixed(0) }} km/h
              </p>
              <p>Wind speed</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.translate-y-0 {
  transform: translateY(0);
}
.translate-y-4 {
  transform: translateY(4rem);
}
.opacity-100 {
  opacity: 1;
}
.opacity-0 {
  opacity: 0;
}
</style>
