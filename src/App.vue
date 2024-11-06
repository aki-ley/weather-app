<template> 
  <v-app> 
    <v-container class="pa-10">
      <v-row justify="center"> 
        <v-col cols="12" md="6"> 
          <v-card class="pa-4"> 
            <v-card-title> 
              <h2 class="text-center"> <v-icon class="px-7">mdi mdi-weather-cloudy</v-icon>Weather App</h2> 
            </v-card-title> 

            <v-card-text> 
            
              <v-text-field 
                :loading="loading"
                append-inner-icon="mdi-magnify"
                variant="solo"
                hide-details
                single-line
                @click:append-inner="onClick"
                v-model="newCity" 
                label="Enter city" 
                @keyup.enter="addCity" 
              >
              </v-text-field> 

              <div class="text-center py-5">
                <v-btn color="teal-lighten-2" @click="addCity" class="rounded-lg"> <v-icon class="px-4">mdi mdi-cloud-search-outline</v-icon>
                  Add City 
                </v-btn> 
              </div>

              <v-alert v-if="error" type="error">{{ error }}</v-alert> 

              <v-card v-for="(weather, index) in weatherData" :key="index" class="mt-4"> 
                <v-card-title class="text-h5">{{ weather.name }}</v-card-title> 
                <v-card-subtitle> 
                  {{ weather.weather[0].description }} 
                </v-card-subtitle> 
                <v-card-text> 
                  <p><strong>Temperature:</strong> {{ weather.main.temp }} °C</p> 
                  <p><strong>Humidity:</strong> {{ weather.main.humidity }}%</p> 
                  <p><strong>Pressure:</strong> {{ weather.main.pressure }} hPa</p>
                  <p><strong>Wind Speed:</strong> {{ weather.wind.speed }} m/s</p> 
                  <p><strong>Sunrise:</strong> {{ formatTime(weather.sys.sunrise) }}</p>
                  <p><strong>Sunset:</strong> {{ formatTime(weather.sys.sunset) }}</p>
                </v-card-text> 
              </v-card> 
            </v-card-text> 
          </v-card> 
        </v-col> 
      </v-row> 
    </v-container> 
  </v-app> 
</template> 

<script> 
export default { 
  data() { 
    return { 
      newCity: '', 
      cities: [], 
      weatherData: [], 
      error: '' 
    }; 
  }, 
  methods: { 
    async fetchWeather(city) { 
      try { 
        const apiKey = '4dabbaf75b48b31394322ab2e7ed54eb'; // Replace with your OpenWeatherMap API key 
        const response = await fetch( 
          `https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&appid=${apiKey}` 
        ); 
        const data = await response.json(); 
        if (data.cod === 200) { 
          this.weatherData.push(data); 
          this.error = ''; 
        } else { 
          this.error = `City ${city} not found.`;
        } 
      } catch (error) { 
        this.error = `Unable to fetch weather data for ${city}.`; 
      } 
    },
    addCity() {
      if (this.newCity.trim() === '') {
        this.error = 'Please enter a city name.';
        return;
      }
      this.cities.push(this.newCity);
      this.fetchWeather(this.newCity);
      this.newCity = '';
    },
    formatTime(timestamp) {
      const date = new Date(timestamp * 1000);
      return date.toLocaleTimeString();
    }
  } 
}; 
</script> 

<style> 
body { 
  font-family: 'Roboto', sans-serif; 
} 
</style>