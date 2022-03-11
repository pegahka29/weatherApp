<template>
  <q-page class="flex column bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input @keyup.enter="getWeatherBySearch" v-model="search" placeholder="search" dark borderless>
        <template v-slot:before>
          <q-icon @click="getLocation" name="my_location" />
        </template>
        <template v-slot:hint> Field hint </template>
        <template v-slot:append>
          <q-btn round dense flat icon="search" @click="getWeatherBySearch" />
        </template>
      </q-input>
    </div>
    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">{{weatherData.name}}</div>
        <div class="text-h6 text-weight-light">{{weatherData.weather[0].main}}</div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp)  }}</span>
          <span class="text-h4 relative-position degree">&deg;C </span>
        </div>
      </div>
      <div class="text-center col">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" />
      </div>
    </template>
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">Weather App</div>
        <q-btn class="col" flat @click="getLocation">
          <q-icon left size="3em" name="my_location" />
          <div>Find my location</div>
        </q-btn>
      </div>
    </template>
    <div class="col skyline"></div>
  </q-page>
</template>

<script>
import { QSpinnerHourglass } from 'quasar'

export default {
  name: "PageIndex",
  data() {
    return {
      search: "",
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: "https://api.openweathermap.org/data/2.5/weather",
      apiKey: "Your KeyAPI",
    };
  },
  computed: {
    bgClass() {
      if(this.weatherData){
        if(this.weatherData.weather[0].icon.endsWith('n')) {
          return 'bg-night'
        }
        else {
          return 'bg-day'
        }
      }
    }
  },
  methods: {
    getLocation() {
      this.$q.loading.show({
        spinner: QSpinnerHourglass,
        spinnerColor: 'indigo-10',
        spinnerSize: 140,
        backgroundColor: 'dark',
        message: 'please wait...',
        messageColor: 'white'
      })
      navigator.geolocation.getCurrentPosition((position) => {
        this.lat = position.coords.latitude
        this.lon = position.coords.longitude
        this.getWeatherByCoords()
      });
    },
    getWeatherByCoords() {
      this.$q.loading.show({
        spinner: QSpinnerHourglass,
        spinnerColor: 'indigo-10',
        spinnerSize: 140,
        backgroundColor: 'dark',
        message: 'please wait...',
        messageColor: 'white'
      })
      this.$axios(
        `${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`
      ).then((response) => {
      this.weatherData = response.data
        this.timer = setTimeout(() => {
          this.$q.loading.hide()
          this.timer = void 0
        }, 100)      });
    },
    getWeatherBySearch() {
      this.$q.loading.show({
        spinner: QSpinnerHourglass,
        spinnerColor: 'indigo-10',
        spinnerSize: 140,
        backgroundColor: 'dark',
        message: 'please wait...',
        messageColor: 'white'
      })
      this.$axios(
        `${this.apiUrl}?q=${this.search}&appid=${this.apiKey}&units=metric`
      ).then((response) => {
        this.weatherData = response.data
        this.timer = setTimeout(() => {
          this.$q.loading.hide()
          this.timer = void 0
        }, 100)      });
    }
  },

  beforeDestroy () {
    if (this.timer !== void 0) {
      clearTimeout(this.timer)
      this.$q.loading.hide()
    }
  }
};
</script>

<style lang="sass">
.q-page
  background: linear-gradient(to bottom, #b993d6, #8ca6db)
  &.bg-night
  background-image: linear-gradient(to bottom, #1330ed, #2c2eec, #3c2ceb, #4829ea, #5227e9, #5428ea, #5628ea, #5829eb, #532dee, #4d30f1, #4634f3, #3f37f6)
  &.bg-day
  background: linear-gradient(to bottom, #36d1dc, #5b86e5)
.degree
  top: -44px

.skyline
  flex: 0 0 100px
  background: url("../assets/img/skyline.png")
  background-size: contain
  background-position: center bottom

</style>
