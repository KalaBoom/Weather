<template>
    <div class="weather-data">
      <div class="weather-data__name">
        {{weatherData.name}}
      </div>
      <div class="weather-data__temp">
        {{Math.round(weatherData.main.temp)}}&#176;
      </div>
      <div class="weather-data__date">
        <div class="time">{{date.time}}</div>
        <div class="day">{{date.day}}</div>
      </div>
      <div class="weather-data__item">
        <div class="weather-data__item__left">
          <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}.png`" class="icon">
        </div>
        <div class="weather-data__item__rigth">
          <div class="title">{{condition.title}}</div>
          <div class="description" v-if="condition.title !== condition.description">{{condition.description}}</div>
        </div>
      </div>
      <div class="weather-data__item">
        <div class="weather-data__item__left">
          <pressure class="icon" />
        </div>
        <div class="weather-data__item__rigth">
          <div class="title">Давление</div>
          <div class="description">{{weatherData.main.pressure}} Па</div>
        </div>
      </div>
      <div class="weather-data__item">
        <div class="weather-data__item__left">
          <humidity class="icon" />
        </div>
        <div class="weather-data__item__rigth">
          <div class="title">Влажность</div>
          <div class="description">{{weatherData.main.humidity}} %</div> 
        </div>
      </div>
      <div class="weather-data__item">
        <div class="weather-data__item__left">
          <wind class="icon" />
        </div>
        <div class="weather-data__item__rigth">
          <div class="title">Ветер</div>
          <div class="description">{{Math.round(weatherData.wind.speed)}} км/ч</div> 
        </div>
      </div>
    </div>
</template>

<script>
import moment from 'moment'
import conditions from '@/json/conditions.json'
import pressure from '@/assets/img/pressure.vue'
import humidity from '@/assets/img/humidity.vue'
import wind from '@/assets/img/wind.vue'
export default {
  props: {
      weatherData: {
          type: Object,
          require: true
      },
      isMain: {
        type: Boolean,
        require: false,
        default: false
      }
  },
  data() {
      return {
          conditions,
          date: {
            day: moment().format('L'),
            time: moment().format('HH:mm')
          },
          condition: {
            title: '',
            description: ''
          }
      }
  },
  components: {
    pressure, humidity, wind
  },
  mounted() {
    this.setCondition()
    if(!this.isMain) {
      const newDate = moment(this.weatherData.dt*1000)
      this.date = {
        day: newDate.format('L'),
        time: newDate.format('HH:mm')
      }
    } else {
      this.setTime()
    }
  },
  methods: {
    moment,
    setTime() {
      setInterval(() => {
        this.date.time = moment().format('HH:mm')
      }, 6000)
    },
    setCondition() {
      const title = this.weatherData.weather[0].main.toLowerCase()
      const description = this.weatherData.weather[0].description.toLowerCase()
      this.condition = {
        title: this.conditions.main[title],
        description: this.conditions.description[description]
      }
    }
  }
}
</script>

<style lang="scss">
.weather-data {
  position: relative;
  border: 1px solid teal;
  border-radius: 50px;
  height: 70%;
  width: 300px;
  color: rgb(0, 0, 0);
  padding: 20px 10px;
  text-align: center;
  margin-right: 20px;
  padding: 5px;
  &__name {
    font-size: 30px;
  }
  &__temp {
    font-size: 50px;
    margin-top: 20px;
  }
  &__date {
    font-size: 20px;
    margin-top: 15px;
    .time {
      margin-bottom: 10px;
    }
  }
  &__item {
    display: flex;
    padding-left: 55px;
    margin-top: 20px;
    &__left {
      margin-right: 20px;
      width: 50px;
      .icon {
        width: 100%;
      }
    }
    &__rigth {
      text-align: left;
      .title {
        font-size: 20px;
        margin-bottom: 5px;
        text-transform: capitalize;
      }
      .description {
        font-size: 20px;
      }
    }
  }
}
</style>