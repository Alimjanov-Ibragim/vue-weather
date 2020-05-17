<template>
  <div
    id="app"
    :class="
      typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : ''
    "
  >
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keydown.enter="fetchWeather"
        />
        <button class="search-button" @click="fetchWeather">
          <svg
            fill="#313131"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 128 128"
            width="64px"
            height="64px"
          >
            <path
              d="M 52.349609 14.400391 C 42.624609 14.400391 32.9 18.1 25.5 25.5 C 10.7 40.3 10.7 64.399219 25.5 79.199219 C 32.9 86.599219 42.600391 90.300781 52.400391 90.300781 C 62.200391 90.300781 71.900781 86.599219 79.300781 79.199219 C 94.000781 64.399219 93.999219 40.3 79.199219 25.5 C 71.799219 18.1 62.074609 14.400391 52.349609 14.400391 z M 52.300781 20.300781 C 60.500781 20.300781 68.700391 23.399219 74.900391 29.699219 C 87.400391 42.199219 87.4 62.5 75 75 C 62.5 87.5 42.199219 87.5 29.699219 75 C 17.199219 62.5 17.199219 42.199219 29.699219 29.699219 C 35.899219 23.499219 44.100781 20.300781 52.300781 20.300781 z M 52.300781 26.300781 C 45.400781 26.300781 38.9 29 34 34 C 29.3 38.7 26.700391 44.800391 26.400391 51.400391 C 26.300391 53.100391 27.600781 54.4 29.300781 54.5 L 29.400391 54.5 C 31.000391 54.5 32.300391 53.199609 32.400391 51.599609 C 32.600391 46.499609 34.699219 41.799219 38.199219 38.199219 C 41.999219 34.399219 47.000781 32.300781 52.300781 32.300781 C 54.000781 32.300781 55.300781 31.000781 55.300781 29.300781 C 55.300781 27.600781 54.000781 26.300781 52.300781 26.300781 z M 35 64 A 3 3 0 0 0 32 67 A 3 3 0 0 0 35 70 A 3 3 0 0 0 38 67 A 3 3 0 0 0 35 64 z M 83.363281 80.5 C 82.600781 80.5 81.850781 80.800391 81.300781 81.400391 C 80.100781 82.600391 80.100781 84.499609 81.300781 85.599609 L 83.800781 88.099609 C 83.200781 89.299609 82.900391 90.6 82.900391 92 C 82.900391 94.4 83.8 96.700391 85.5 98.400391 L 98.300781 111 C 100.10078 112.8 102.39922 113.69922 104.69922 113.69922 C 106.99922 113.69922 109.29961 112.79961 111.09961 111.09961 C 114.59961 107.59961 114.59961 101.90039 111.09961 98.400391 L 98.300781 85.599609 C 96.600781 83.899609 94.300391 83 91.900391 83 C 90.500391 83 89.2 83.300391 88 83.900391 L 85.5 81.400391 C 84.9 80.800391 84.125781 80.5 83.363281 80.5 z M 91.900391 88.900391 C 92.700391 88.900391 93.5 89.200781 94 89.800781 L 106.69922 102.5 C 107.89922 103.7 107.89922 105.59922 106.69922 106.69922 C 105.49922 107.89922 103.6 107.89922 102.5 106.69922 L 89.800781 94.099609 C 89.200781 93.499609 88.900391 92.700391 88.900391 91.900391 C 88.900391 91.100391 89.200781 90.300781 89.800781 89.800781 C 90.400781 89.200781 91.100391 88.900391 91.900391 88.900391 z"
            />
          </svg>
        </button>
      </div>

      <h1
        v-if="!weather.main && weather.cod != 404 && !load"
        class="query-text"
      >
        Enter your request...
      </h1>

      <h1 v-if="weather.cod == 404 && !load" class="query-text">
        City not found
      </h1>

      <div v-if="load" class="load">
        <div class="lds-default">
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
          <div></div>
        </div>
      </div>

      <div
        class="weather-wrap"
        v-if="typeof weather.main != 'undefined' && !load"
      >
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">
            {{ Math.round(weather.main.temp) }}°c
            <div>
              <img :src="iconUrl + weather.weather[0].icon + '@2x.png'" />
            </div>
          </div>
          <div class="weather">{{ weather.weather[0].main }}</div>
          <div class="weather-options">
            <h3>More:</h3>
            <p>
              Timezone: <strong>{{ getTimezone(weather.timezone) }}</strong>
            </p>
            <p>
              Feels like:
              <strong>{{ Math.round(weather.main.feels_like) }} °c</strong>
            </p>
            <p>
              Humidity: <strong>{{ weather.main.humidity }} °c</strong>
            </p>
            <p>
              Pressure: <strong>{{ weather.main.pressure }} mBar</strong>
            </p>
            <p>
              Wind:
              <strong>
                <div>{{ getWind(weather.wind.deg) }}</div>
                {{ Math.round(weather.wind.speed) }} m/s</strong
              >
            </p>
            <p>
              Sunrise: <strong>{{ getTime(weather.sys.sunrise) }}</strong>
            </p>
            <p>
              Sunset: <strong>{{ getTime(weather.sys.sunset) }}</strong>
            </p>
          </div>
        </div>
      </div>
    </main>
    <div class="developers">
      Developers: <br />
      Nurdinova R. & Alimjanova A.
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      api_key: "40c828b7469115f0880cd746da4a4796",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      iconUrl: "https://openweathermap.org/img/wn/",
      load: false
    };
  },
  // created: () => {
  //   return fetchWeather();
  // },
  methods: {
    fetchWeather() {
      // if (e.key == "Enter") {
      this.load = true;
      fetch(
        `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
      )
        .then(res => {
          return res.json();
        })
        .then(this.setResults)
        .catch(error => {
          console.log(error);
        });
      // }
    },
    setResults(results) {
      this.load = false;
      this.weather = results;
    },
    getWind(deg) {
      if (deg > 11.25 && deg < 33.75) {
        return "North-northeast";
      } else if (deg > 33.75 && deg < 56.25) {
        return "East-northeast";
      } else if (deg > 56.25 && deg < 78.75) {
        return "East";
      } else if (deg > 78.75 && deg < 101.25) {
        return "East-southeast";
      } else if (deg > 101.25 && deg < 123.75) {
        return "East-southeast";
      } else if (deg > 123.75 && deg < 146.25) {
        return "Southeast";
      } else if (deg > 146.25 && deg < 168.75) {
        return "South-southeast";
      } else if (deg > 168.75 && deg < 191.25) {
        return "South";
      } else if (deg > 191.25 && deg < 213.75) {
        return "South-southwest";
      } else if (deg > 213.75 && deg < 236.25) {
        return "Southwest";
      } else if (deg > 236.25 && deg < 258.75) {
        return "West-southwest";
      } else if (deg > 258.75 && deg < 281.25) {
        return "West";
      } else if (deg > 281.25 && deg < 303.75) {
        return "West-northwest";
      } else if (deg > 303.75 && deg < 326.25) {
        return "Northwest";
      } else if (deg > 326.25 && deg < 348.75) {
        return "North-northwest";
      } else {
        return "North";
      }
    },
    getTimezone(item) {
      var timezone = item / 3600;
      var el = timezone.toString().split("")[0];
      var res = el == "-" ? "GMT / UTC " : "GMT / UTC +";
      return res + timezone;
    },
    getTime(item) {
      var date = new Date(item * 1000);
      // Hours part from the timestamp
      var hours = date.getHours();
      var minutes = date.getMinutes();
      var ampm = hours >= 12 ? "PM" : "AM";
      hours = hours % 12;
      hours = hours ? hours : 12; // the hour '0' should be '12'
      minutes = minutes < 10 ? "0" + minutes : minutes;
      var strTime = hours + ":" + minutes + " " + ampm;
      return strTime;
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
        "December"
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday"
      ];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    }
  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: "montserrat", sans-serif;
}
#app {
  background-image: url("./assets/cold-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
  max-width: 400px;
  margin: 0 auto;
}
#app.warm {
  background-image: url("./assets/warm-bg.jpg");
}
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}
.search-box {
  width: 100%;
  margin-bottom: 30px;
  display: flex;
}
.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}
.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}
.search-box .search-button {
  margin-left: 10px;
  background: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
  cursor: pointer;
  outline: none;
}
.search-box .search-bar:focus + .search-button,
.search-button:focus ~ .search-bar {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}
.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.location-box .date,
.developers {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}
.developers {
  border-top: 1px solid #fff;
  padding: 10px;
  background: #000;
}
.weather-box {
  text-align: center;
}
.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather,
.query-text {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.query-text {
  text-align: center;
  padding-top: 20px;
}
.weather-options {
  color: #ffffff;
  text-align: left;
  max-width: 80%;
  margin: 20px auto 0;
  font-size: 18px;
}
.weather-options h3 {
  margin-bottom: 10px;
  font-size: 24px;
}
.weather-options p {
  margin-bottom: 10px;
  display: flex;
  justify-content: space-between;
}
.weather-options p strong {
  font-size: 20px;
  text-align: right;
}
.lds-default {
  display: inline-block;
  position: relative;
  width: 80px;
  height: 80px;
}
.lds-default div {
  position: absolute;
  width: 6px;
  height: 6px;
  background: #fff;
  border-radius: 50%;
  animation: lds-default 1.2s linear infinite;
}
.lds-default div:nth-child(1) {
  animation-delay: 0s;
  top: 37px;
  left: 66px;
}
.lds-default div:nth-child(2) {
  animation-delay: -0.1s;
  top: 22px;
  left: 62px;
}
.lds-default div:nth-child(3) {
  animation-delay: -0.2s;
  top: 11px;
  left: 52px;
}
.lds-default div:nth-child(4) {
  animation-delay: -0.3s;
  top: 7px;
  left: 37px;
}
.lds-default div:nth-child(5) {
  animation-delay: -0.4s;
  top: 11px;
  left: 22px;
}
.lds-default div:nth-child(6) {
  animation-delay: -0.5s;
  top: 22px;
  left: 11px;
}
.lds-default div:nth-child(7) {
  animation-delay: -0.6s;
  top: 37px;
  left: 7px;
}
.lds-default div:nth-child(8) {
  animation-delay: -0.7s;
  top: 52px;
  left: 11px;
}
.lds-default div:nth-child(9) {
  animation-delay: -0.8s;
  top: 62px;
  left: 22px;
}
.lds-default div:nth-child(10) {
  animation-delay: -0.9s;
  top: 66px;
  left: 37px;
}
.lds-default div:nth-child(11) {
  animation-delay: -1s;
  top: 62px;
  left: 52px;
}
.lds-default div:nth-child(12) {
  animation-delay: -1.1s;
  top: 52px;
  left: 62px;
}
@keyframes lds-default {
  0%,
  20%,
  80%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.5);
  }
}
.load {
  text-align: center;
}
</style>
