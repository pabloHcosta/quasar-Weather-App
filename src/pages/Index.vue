<template>
  <q-page class="flex column" >
    <div class="col q-pt-lg q-px-md">
      <q-input
      v-model="search"
      @keyup.enter="getWeatherBySearch()"
      placeholder="Search city"
      dark
      borderless
      >
      <template v-slot:before>
        <q-icon
          @click="getLocation"
          name ="my_location"/>
      </template>

       <template v-slot:append>
        <q-btn
         @click="getWeatherBySearch()"
          round
          dense
          flat
          icon="search"
        />
      </template>
      </q-input>
    </div>
    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{ weatherData.name }}
        </div>
        <div class="text-h6 text-weight-light">
          {{ weatherData.weather[0].main}}
        </div>
        <div class="text-h1 text-weight-thin
        q-ma-lg relative-position "
        >
        <span> {{ Math.round(weatherData.main.temp)}}</span>
        <span class=" text-h4 relative-position degree">&deg;</span>
        </div>
        <div class="text-h6 text-weight-light">
          <span>L: {{ Math.round(weatherData.main.temp_min)}}&deg;</span>
          <span>  -  </span>
          <span>H: {{ Math.round(weatherData.main.temp_max)}}&deg;</span>
        </div>
        <div class="col text-center">
          <img :src="`http://openweathermap.org/img/wn/${ weatherData.weather[0].icon }@2x.png`">
       </div>
      </div>
        <q-separator color="white"/>
    </template>
    <div class="col city">
    </div>
  </q-page>
</template>

<script>
import axios from 'axios'
export default {
  name: 'PageIndex',
  data () {
    return {
      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      apiKey: '7e2cf490879455b7214e9ba5390dc103'
    }
  },

  mounted () {
    this.getLocation()
  },

  methods: {
    getLocation () {
      if (this.$q.platform.is.electron) {
        this.$axios.get('https://freegeoip.app/json/')
          .then(response => {
            this.lat = response.data.latitude
            this.lon = response.data.longitude
            this.getWeatherByCoords()
          })
      } else {
        navigator.geolocation.getCurrentPosition(position => {
          this.lat = position.coords.latitude
          this.lon = position.coords.longitude
          this.getWeatherByCoords()
        })
      }
    },

    getWeatherByCoords () {
      axios
        .get(`${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric&lang=pt_br`)
        .then(response => (this.weatherData = response.data))
      console.log(this.weatherData)
    },

    getWeatherBySearch () {
      axios
        .get(`${this.apiUrl}?q=${this.search}&appid=${this.apiKey}&units=metric&lang=pt_br`)
        .then(response => (this.weatherData = response.data))
    }
  }
}
</script>

<style lang="sass">
.q-page
  background: linear-gradient(to bottom, #136a8a, #267871 )
  .degree
    top: -44px
  .city
    flex: 0 0 100px
    background: url(../assets/city.png)
    background-size: contain
    background-position: center bottom
</style>
