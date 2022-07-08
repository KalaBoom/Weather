<template>
  <div v-if="weatherData" class="main">
    <weather-map 
      @click-city="handlerClickCity"
    />
    <weather-view 
      :weatherData="weatherData"
      :isMain="true"
      class="main-data"
    />
    <div class="forecast">
      <div class="forecast__item" 
        v-for="item in listForecast"
        :key="item.dt"
      >
        <weather-view
          :weatherData="item"
        />
      </div>
    </div>
  </div>
  <loader class="main-loader" v-else />
</template>

<script>
import { mapGetters } from 'vuex'
import loader from '@/components/Loader.vue'
import WeatherMap from '@/components/WeatherMap.vue'
import WeatherView from '@/components/WeatherView.vue'

export default {
  name: 'MainView',
  data() {
    return {
      weatherData: null,
      listForecast: null
    }
  },
  mounted() {
    this.getWeatherData()
  },
  components: { 
    loader, WeatherMap,
    WeatherView
  },
  computed: {
    ...mapGetters(['key'])
  },
  methods: {
    getWeatherData() {
      let city = 'Москва'
      if (this.weatherData)
        city = this.weatherData.name
      fetch(`http://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&lang=ru&appid=${this.key}`)
        .then(res => res.json())
        .then(json => {
          this.weatherData = json
          this.getCoords()
        })
    },
    getCoords() {
      let coords = {}
      fetch(`http://api.openweathermap.org/geo/1.0/direct?q=${this.weatherData.name}&limit=1&appid=${this.key}`)
          .then(res => res.json())
          .then(json => coords = {
              lat: json[0].lat,
              lon: json[0].lon
          })
          .then(() => {
            this.getForecast(coords)
          })
    },
    getForecast(coords) {
      fetch(`http://api.openweathermap.org/data/2.5/forecast?lat=${coords.lat}&lon=${coords.lon}&units=metric&appid=${this.key}`)
        .then(res => res.json())
        .then(json => this.listForecast = json.list)
    },
    handlerClickCity(coords) {
      fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${coords.lat}&lon=${coords.lng}&units=metric&cnt=1&lang=ru&appid=${this.key}`)
        .then(res => res.json())
        .then(json => this.weatherData = json)
        .then(() => {
          coords.lon = coords.lng
          this.getForecast(coords)
        })
        
    }
  }
}
</script>

<style lang="scss">
.main {
  width: 98vw;
  height: 80vh;
  margin: 0px auto;
}
.main-loader {
  top: 30vh;
  left: 50%;
}
.main-data {
  margin-left: 50px;
}
.forecast {
  display: flex;
  margin: 20px auto 10px;
  overflow: auto;
  z-index: 2;
  position: relative;
  height: 300px;
  width: 90vw;
  &__item {
    height: 100px;
    .weather-data {
      width: auto;
      height: auto;
      &__name {
        font-size: 20px;
      }
      &__temp {
        font-size: 20px;
        margin-top: 10px;
      }
      &__date {
        font-size: 10px;
        margin-top: 5px;
        .time {
          margin-bottom: 5px;
        }
      }
      &__item {
        padding-left: 10px;
        margin-top: 5px;
        &__left {
          margin-right: 5px;
          width: 25px;
        }
        &__rigth {
          .title {
            font-size: 15px;
            margin-bottom: 5px;
          }
          .description {
            font-size: 12px;
          }
        }
      }
    } 
  }
}
</style>