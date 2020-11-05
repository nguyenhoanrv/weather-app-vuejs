<template>
  <div id="app">
    <h1 class="title">weather</h1>
    <div class="search" :class="{dilen : this.is_enter1}">
      <input type="text" placeholder="Enter city" @keypress="fetchWeather" v-model="query" />
      <font-awesome-icon :icon="['fa','search']" class="search-icon" />
    </div>
    <div class="result error" v-if="is_error">Sorry, the specified city was not found...</div>
    <div class="result" v-if="is_enter">
      <div class="info">
        <div class="location">{{weather.name}}, {{weather.sys.country}}</div>
        <div class="time">{{dateBuilder()}}</div>
      </div>

      <div class="parameters">
        <div class="weather">
          <font-awesome-icon :icon="getIcons()" size="6x" class="weather-icon" />
          <div class="weather-description">
            <div class="temp">{{Math.floor(weather.main.temp)}}°c</div>
            <p>{{weather.weather[0].description}}</p>
          </div>
        </div>
        <div class="weather-detait">
          <div class="box">
            <h4>{{weather.main.temp_max}}°</h4>
            <span>Hight</span>
          </div>
          <div class="box">
            <h4>{{weather.wind.speed}}mph</h4>
            <span>Wind</span>
          </div>
          <div class="box">
            <h4>{{getHours(weather.sys.sunrise, weather.timezone)}}</h4>
            <span>Sunrise</span>
          </div>
          <div class="box">
            <h4>{{weather.main.temp_min}}°</h4>
            <span>Low</span>
          </div>
          <div class="box">
            <h4>{{weather.main.humidity}}%</h4>
            <span>Humidity</span>
          </div>
          <div class="box">
            <h4>{{getHours(weather.sys.sunset, weather.timezone)}}</h4>
            <span>Sunset</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      api_key: "37b1231e27ba68ddc188703d4c21f117",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: null,
      is_enter: false,
      is_error: false,
      is_enter1: false,
    };
  },
  methods: {
    fetchWeather(e) {
      if (e.key === "Enter") {
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        )
          .then((res) => {
            if (res.ok) return res.json();
            throw Error(res.statusText);
          })
          .then((rs) => {
            this.weather = rs;
            this.getIcons(this.weather.main);
            this.is_enter = true;
            this.is_error = false;
            this.is_enter1 = true;
          })
          .catch(() => {
            this.is_error = true;
            this.is_enter = false;
            this.is_enter1 = true;
          });
      }
    },
    getIcons(main) {
      if (main === "Thunderstorm") {
        return ["fas", "bolt"];
      } else if (main === "Drizzle") {
        return ["fas", "cloud-rain"];
      } else if (main === "Rain") {
        return ["fas", "cloud-showers-heavy"];
      } else if (main === "Snow") {
        return ["fas", "snowflake"];
      } else if (main === "Clear") {
        return ["fas", "sun"];
      } else if (main === "Clouds") {
        return ["fas", "cloud"];
      } else {
        return ["fas", "smog"];
      }
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
    getHours(secs, timezone) {
      return `${Math.floor(((secs + timezone) / 3600) % 24)}:${
        Math.floor((secs + timezone) / 60) % 60
      }`;
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Recursive", sans-serif;
}
html,
body {
  height: 100%;
}
#app {
  width: 100%;
  height: 100%;
  background-image: url("./background.jpg");
  background-position: center;
  background-size: cover;
}
.title {
  position: absolute;
  width: 200px;
  left: 50%;
  transform: translateX(-100px);
  margin-top: 50px;
  text-transform: uppercase;
  font-size: 50px;
}
.search {
  position: absolute;
  width: 400px;
  height: 46px;
  top: calc(45% - 46px);
  left: calc(50% - 200px);
  transition: 0.5s all ease-in;
}
.search > input {
  width: 400px;
  height: 46px;
  outline: none;
  border-radius: 25px;
  border: 1px solid #ebebeb;
  padding: 15px 20px;
  font-size: 20px;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px,
    rgba(0, 0, 0, 0.06) 0px 2px 4px -1px;
}
.search > input:focus {
  box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px,
    rgba(0, 0, 0, 0.05) 0px 4px 6px -2px;
}
.search-icon {
  position: absolute;
  right: 0;
  margin: 15px 8px;
  color: #dbd8d8;
}
.result {
  width: 70%;
  position: absolute;
  top: 40%;
  left: 15%;
}
.result .info .location {
  color: #141414d9;
  font-weight: 600;
  font-size: 32px;
}
.time {
  font-size: 20px;
}
.result .parameters {
  margin-top: 20px;
  display: flex;
  align-items: center;
  justify-content: space-around;
}
.weather {
  display: flex;
  align-items: center;
  color: #a3a3a3;
}

.weather-description {
  width: 50%;
  font-size: 50px;
  padding: 15px;
}
.weather-description > p {
  font-size: 25px;
  text-transform: uppercase;
}
.weather-detait {
  width: 50%;
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  background-color: rgba(0, 0, 0, 0.2);
  border-radius: 20px;
  color: rgba(255, 255, 255, 0.9);
}
.weather-detait .box {
  width: calc(100% / 3);
  text-align: center;
  padding: 28px;
  font-size: 18px;
}
.weather-detait .box h4 {
  margin-bottom: 14px;
}
.dilen {
  transform: translateY(-200px);
}
.error {

  text-align: center;
  font-size: 30px;
  margin: auto;
  background-color: rgba(255, 255, 255, 0.657);
}
</style>
