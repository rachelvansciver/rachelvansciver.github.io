<template>
  <div id="app" :class="typeof data.main != 'undefined' && changeBackground(status)">
    <main>
      <div class="search-box">
        <input type="text" class="search-bar" placeholder="Search a city name and press enter to get the current weather..." v-model="search" @keypress="fetchWeather">
        <input type="radio" id="C"  value="metric" v-model="picked">
        <label for="C">°C</label>
        <br>
        <input type="radio" id="F" value="imperial" v-model="picked">
        <label for="F">°F</label>
        <br>
      </div>
      <div class="weather-wrap" v-if="typeof data.main != 'undefined'">
          <div class="temp">{{temp}}°</div>
        <div class="location-box">
          <div class="location">{{cc}}</div>
          <div class="date">{{time}}</div>
          <div class="status">{{status}}</div>
          <div class="more-weather-info">
            <div class="feels-like">L: {{ lowTemp }}</div>
            <div class="feels-like">H: {{ highTemp }}</div>
            <div class="feels-like">Feels like: {{feelsLike}}°</div>
            <div class="feels-like">Humidity: {{humidity}}%</div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>

import weather from "@/components/weather"
import config from './keys.json'
import moment from 'moment'
export default {
  name: 'app',
  components:
    weather,
  data(){
    return{
      apiKey: config.apiKey,
      base_url: config.base_url,
      picked:'imperial',
      search: '',
      data: {},
      date: "",
      cc: "",
      temp: "",
      status: "",
      time: "",
      feelsLike: "",
      humidity: "",
      lowTemp: "",
      highTemp: "",
    }
  }, methods:{
    fetchWeather(e){
      if(e.key === "Enter"){
        fetch(`${this.base_url}weather?q=${this.search}&units=${this.picked}&APPID=${this.apiKey}`)
            .then(data => {
              return data.json();

            }).then(this.setResults);
      }
    },
    setResults(results){

      this.data = results;
      this.cc = results.name + ", " + results.sys.country;
      this.status = results.weather[0].main;
      this.temp = Math.round(results.main.temp)
      this.feelsLike = Math.round(results.main.feels_like);
      this.humidity = results.main.humidity;
      this.lowTemp = Math.round(results.main.temp_min);
      this.highTemp = Math.round(results.main.temp_max);
      this.time = moment(this.getDate(results)).format("LLLL")

    },
    getDate(args){
      let d = new Date()
      let localTime = d.getTime()
      let localOffset = d.getTimezoneOffset() * 60000
      let utc = localTime + localOffset
      let localTimeZone = utc + (1000 * args.timezone);
      return localTimeZone;
    },
    changeBackground(status){
      switch(status){
        case "Clouds":
          return 'cloudy';
        case "Sunny":
        case "Clear":
          return 'sunny';
        case "Rain":
          return 'rainy'
        default:
          return ''

      }
    }
  }
}
</script>
<style>
body{
  font-family: Arial,serif;
}
#app{
  background-image: url("assets/sunny.png");
  background-size: cover;
  background-position: bottom;

}
#app.cloudy{
  background-image: url("assets/cloudy.jpg");
  background-size: cover;
  background-position: bottom;
  color: aliceblue;
}
#app.rainy{
  background-image: url("assets/rainy.jpg");
  background-size: cover;
  background-position: bottom;
  color: aliceblue;
}
main{
  min-height: 100vh;
  padding: 25px;
}
.search-box{
  margin-bottom: 30px;
  width: 90%;
  display: block;
  width: 90%;
  padding: 15px;
  color: #313131;
  font-size: 100%;
  border: none;
  appearance: none;
  outline: none;
  background-color: rgba(255,255,255,0.5);
  box-shadow: 0px 0 8px rgba(0,0,0, 0.25);
}

.search-bar{
  font-size: 100%;
  display: block;
  padding: 15px;
  background-color: rgba(255,255,255,0.5);
  border: none;
  outline: none;
  width: 98%;
}
.weather-wrap{
  font-family: Raleway;
  font-weight:500;
  font-size: 40px;
  text-shadow: 3px 3px rgba(0,0,0,0.25);
  display: flex;
  height: 600px;
  min-width: 100%;
  align-self: center;
  justify-items: center;
  align-items: center;
}
.temp{
  width: 600px;
  font-size: 300px;
  text-align: center;

}
.location-box{
  background-color: rgba(0,0,0,0.15);
  box-shadow: 5px 10px rgba(0,0,0,0.5);
  box-sizing: border-box;
  margin: 0 50px;
  font-weight: 800;
  font-style: oblique;
  text-align: center;
}
.more-weather-info{
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}
</style>
