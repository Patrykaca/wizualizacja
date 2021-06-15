<template>
  <div id="main">
    <line-chart :data="this.arr" :min="this.d_min" :max="this.d_max"></line-chart>
    <div class="container my-5" style="max-width: 400px; min-width: 360px">
      <form class="search-location" v-on:submit.prevent="getWeather">
        <button class="btn-my" v-on:click="set_max_temp(); citySearch">Hottest city</button>
        <button class="btn-my" v-on:click="set_min_temp(); citySearch">Coldest city</button>
        <button class="btn-my" v-on:click="set_humidity(); citySearch">Higher humidity</button>
        <button class="btn-my" v-on:click="set_pressure(); citySearch">Higher pressure</button>
      </form>
      <p class="text-center my-3" v-if="cityFound">No city found</p>
      <div
          class="card rounded my-3 shadow-lg back-card overflow-hidden"
          v-if="visible"
      >
        <!-- Top of card starts here -->
        <div class="card-top text-center" style="margin-bottom: 15rem">
          <div class="city-name my-3">
            <p>{{ weather.cityName }}</p>
            <span>...</span>
            <p class="">{{ weather.country }}</p>
          </div>
        </div>
        <!-- top of card ends here -->

        <!--card middle body, card body used cos I want to just update the innerHTML -->
        <div class="card-body">
          <!-- card middle starts here -->
          <div class="card-mid">
            <div class="row">
              <div class="col-12 text-center temp">
                <span>{{ weather.temperature }}&deg;C</span>
                <p class="my-4">{{ weather.description }}</p>
              </div>
            </div>
            <div class="row">
              <div class="col d-flex justify-content-between px-5 mx-5">
                <p>
                  <img src="./assets/down.svg" alt=""/>
                  {{ weather.lowTemp }}&deg;C
                </p>
                <p>
                  <img src="./assets/up.svg" alt=""/>
                  {{ weather.highTemp }}&deg;C
                </p>
              </div>
            </div>
          </div>
          <!-- card middle ends here -->

          <!-- card bottom starts here -->
          <div class="card-bottom px-5 py-3 row">
            <div class="col text-center">
              <p>{{ weather.feelsLike }}&deg;C</p>
              <span>Feels like</span>
            </div>
            <div class="col text-center">
              <p>{{ weather.humidity }}%</p>
              <span>humidity</span>
            </div>
          </div>
          <div class="card-bottom px-5 py-3 row">
            <div class="col text-center">
              <p>{{ weather.pressure }}hPa</p>
              <span>pressure</span>
            </div>
          </div>

          <!-- card bottom ends here -->
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import 'chart.js'


export default {
  data() {
    return {
      flag_max_temp: false,
      flag_min_temp: false,
      flag_humidity: false,
      flag_pressure: false,

      d_min: 15,
      d_max: 35,

      arr: [],

      citySearch: "",
      weather: {
        cityName: "Gwarinpa",
        country: "NG",
        temperature: 12,
        description: "Clouds everywhere",
        lowTemp: "19",
        highTemp: "30",
        feelsLike: "20",
        humidity: "55",
        pressure: "55",
      },
    };
  },


  methods: {

    set_max_temp: function () {
      this.flag_max_temp = true;
    },
    set_min_temp: function () {
      this.flag_min_temp = true;
    },
    set_humidity: function () {
      this.flag_humidity = true;
    },
    set_pressure: function () {
      this.flag_pressure = true;
    },

    getHighestTemperature: function (data) {
      let max = data.list[0];
      this.arr = [];
      for (let i = 0; i < 50; i++) {
        let a = [];
        a.push(data.list[i].name, data.list[i].main.temp_max);
        this.arr.push(a);
        if (max.main.temp < data.list[i].main.temp) {
          max = data.list[i];
        }
      }
      this.arr.sort();
      this.d_max = Math.round(max.main.temp + 5);
      this.d_min = Math.round(max.main.temp - 5);
      return max;
    },

    getLowestTemperature: function (data) {
      let max = data.list[0];
      this.arr = [];
      for (let i = 0; i < 50; i++) {
        let a = [];
        a.push(data.list[i].name, data.list[i].main.temp_min);
        this.arr.push(a);
        if (max.main.temp > data.list[i].main.temp) {
          max = data.list[i];
        }
      }
      this.arr.sort();
      this.d_max = Math.round(max.main.temp + 5);
      this.d_min = Math.round(max.main.temp - 5);
      return max;
    },

    getHumidity: function (data) {
      let max = data.list[0];
      this.arr = [];
      for (let i = 0; i < 50; i++) {
        let a = [];
        a.push(data.list[i].name, data.list[i].main.humidity);
        this.arr.push(a);
        if (max.main.humidity < data.list[i].main.humidity) {
          max = data.list[i];
        }
      }
      this.arr.sort();
      this.d_max = Math.round(max.main.humidity + 25);
      this.d_min = Math.round(max.main.humidity - 25);
      return max;
    },

    getPressure: function (data) {
      let max = data.list[0];
      this.arr = [];
      for (let i = 0; i < 50; i++) {
        let a = [];
        a.push(data.list[i].name, data.list[i].main.pressure);
        this.arr.push(a);
        if (max.main.pressure < data.list[i].main.pressure) {
          max = data.list[i];
        }
      }
      this.arr.sort();
      this.d_max = Math.round(max.main.pressure + 75);
      this.d_min = Math.round(max.main.pressure - 75);
      return max;
    },

    getWeather: async function () {
      const key = "86b0a241d38234d9d48e575046166834";
      //const baseURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;
      const closestToLodz = `http://api.openweathermap.org/data/2.5/find?lat=51.5&lon=19.5&cnt=50&appid=${key}&units=metric`;
      //const baseURL = `http://api.openweathermap.org/data/2.5/forecast?id=524901&appid=${key}`;

      //fetch weather
      try {
        const response = await fetch(closestToLodz);
        const data_all = await response.json();

        let data = data_all.list[0];

        if (this.flag_max_temp) {
          data = this.getHighestTemperature(data_all)
          this.flag_max_temp = false;
        }
        if (this.flag_min_temp) {
          data = this.getLowestTemperature(data_all)
          this.flag_min_temp = false;
        }
        if (this.flag_humidity) {
          data = this.getHumidity(data_all)
          this.flag_humidity = false;
        }
        if (this.flag_pressure) {
          data = this.getPressure(data_all)
          this.flag_pressure = false;
        }

        this.citySearch = "";
        this.weather.cityName = data.name;
        this.weather.country = data.sys.country;
        this.weather.temperature = data.main.temp;
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_min);
        this.weather.highTemp = Math.round(data.main.temp_max);
        this.weather.feelsLike = Math.round(data.main.feels_like);
        this.weather.humidity = Math.round(data.main.humidity);
        this.weather.pressure = Math.round(data.main.pressure);

        this.visible = true;
        this.cityFound = false;

      } catch (error) {
        console.log(error);
        this.cityFound = true;
        this.visible = false;
      }
    }
    ,
  }
  ,
}
;
</script>

<style scoped>
@import "./assets/custom.css";
</style>
