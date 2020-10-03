<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input
      v-model="search"
      @keyup.enter="getWeatherBySearch()"
      placeholder="Search"
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
          {{ weatherData.weather[0].description}}
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
      <q-separator  color="white"/>
      <div class="row justify-around q-mx-lg q-mt-md">
        <div class="col-4 text-gray text-weight-light">FEELS</div>
        <div class="col-4 text-gray text-weight-light">HUMIDITY</div>
      </div>
      <div class="row justify-around q-mx-lg q-mb-md ">
        <div class=" col-4 text-h6 text-white text-weight-light">
          <span>{{ Math.round(weatherData.main.feels_like)}}&deg;</span>
        </div>
        <div class=" col-4 text-h6 text-white text-weight-light">
          <span>{{ Math.round(weatherData.main.humidity)}}%</span>
        </div>
      </div>

      <q-separator color="white" inset />

      <div class="row justify-around q-mx-lg q-mt-md">
        <div class="col-4 text-gray text-weight-light">WIND</div>
        <div class="col-4 text-gray text-weight-light">PRESSURE</div>
      </div>
      <div class="row justify-around q-mx-lg ">
        <div class=" col-4 text-h6 text-white text-weight-light">
          <span>{{ Math.round(weatherData.wind.speed)}} km\hr</span>
        </div>
        <div class=" col-4 text-h6 text-white text-weight-light">
          <span>{{ Math.round(weatherData.main.pressure)}} hPa</span>
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
      sunriseTime: null,
      sunsetTime: null,
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      apiKey: '7e2cf490879455b7214e9ba5390dc103'
    }
  },
  computed: {
    bgClass () {
      var result = ''
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith('n')) {
          result = 'bg-night'
        } else {
          result = 'bg-day'
        }
        console.log(result)
      }
      return result
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
        .get(`${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`)
        .then(response => (this.weatherData = response.data))
    },

    getWeatherBySearch () {
      axios
        .get(`${this.apiUrl}?q=${this.search}&appid=${this.apiKey}&units=metric`)
        .then(response => (this.weatherData = response.data))
    }
  }
}
</script>

<style lang="sass">
.q-page
  background: linear-gradient(to right, #2980b9, #6dd5fa, #ffffff)
  &.bg-night
  background: linear-gradient(to right, #000000, #434343)
  &.bg-day
  background: linear-gradient(to right, #007991, #78ffd6)
  .degree
    top: -44px
</style>
