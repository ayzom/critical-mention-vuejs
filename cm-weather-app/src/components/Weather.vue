<template>
  <div>
    <v-system-bar
        color="primary"
        lights-out
        height="46px"
      >
        <h2>Weather Forecast
          <img src="../assets/frost.png" alt="">
        </h2>
        <v-spacer></v-spacer>
      </v-system-bar>

    <div class="mt-top">
      <form class="input" action="">
        <input type="text" v-model="userLocation" class="inputValue" @keydown.enter.prevent="getWeatherByLocation" placeholder="Enter a city">
        <button type="button" class="searchBtn" @click.prevent="getWeatherByLocation">Search</button>
      </form>
    </div>
  <v-container v-if="isData">
    <div class="mt-top-v1">
      <v-card
        class="mx-auto"
        max-width="344"
        outlined
      >
        <v-list-item three-line>
          <v-list-item-content>
            <div class="overline mb-4">
              <h1 style="display:inline">{{this.name}}</h1>, {{this.country}}
            </div>
            <v-list-item-title class="headline mb-1">
              {{this.temp}}
            </v-list-item-title>
            <v-list-item-subtitle>{{this.min}} / {{this.max}} {{this.dayToday}}</v-list-item-subtitle>
            <v-list-item-subtitle>{{this.desc}}</v-list-item-subtitle>
            
          </v-list-item-content>
        </v-list-item>

      </v-card>
    </div>


    <v-container class="mt-top grey lighten-5">
        <v-row no-gutters>
          <v-col>
            <v-card
              class="pa-2"
              outlined
              tile
            >
            <div>
              {{this.feel}}
            </div>
              <div>
                Real feel
              </div>
            </v-card>
          </v-col>
          <v-col>
            <v-card
              class="pa-2"
              outlined
              tile
            >
            <div>
              {{this.wind}} km/h
            </div>
              <div>
                NNW wind
              </div>
            </v-card>
          </v-col>
          <v-col>
            <v-card
              class="pa-2"
              outlined
              tile
            >
            <div>
              {{this.humid}} %
            </div>
              <div>
                Humidity
              </div>
            </v-card>
          </v-col>
          <v-col>
            <v-card
              class="pa-2"
              outlined
              tile
            >
            <div>
              {{this.pressure}} hPa
            </div>
              <div>
                Pressure
              </div>
            </v-card>
          </v-col>
        </v-row>
    </v-container>
  </v-container>

  <v-container v-else>
    <div class="skeleton-top">
            <v-skeleton-loader
              class="mx-auto"
              max-width="300"
              type="card"
              height=160
            ></v-skeleton-loader>
    </div>
    <div class="mt-top-v1">

        <v-row no-gutters>
          <v-col>
            <v-skeleton-loader
              class="mx-auto"
              max-width="300"
              type="card"
              height=60
            ></v-skeleton-loader>
          </v-col>
          <v-col>
            <v-skeleton-loader
              class="mx-auto"
              max-width="300"
              type="card"
              height=60
            ></v-skeleton-loader>
          </v-col>
          <v-col>
            <v-skeleton-loader
              class="mx-auto"
              max-width="300"
              type="card"
              height=60
            ></v-skeleton-loader>
          </v-col>
          <v-col>
            <v-skeleton-loader
              class="mx-auto"
              max-width="300"
              type="card"
              height=60
            ></v-skeleton-loader>
          </v-col>
        </v-row>

    </div>
  </v-container>

  </div>  
</template>

<script>
  import axios from 'axios';
  
  export default {
    name: 'HelloWorld',
    components: {

    },
    data: () => {
      return {
        userLat:"",
        userLong: "",
        userLocation: "",
        direction: "",
        wind: "",
        feel: "",
        humid: "",
        pressure: "",
        name: "",
        temp: "",
        description: "",
        max: "",
        min: "",
        country: "",
        desc: "",
        day: "",
        img: "",
        currDate: new Date(),
        weekdays: ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"],
        dayToday: "",
        isData: false,
        isLatLongSet: false
      }
    },
    mounted() {

     this.dayToday = this.weekdays[this.currDate.getDay()];
      if (navigator.geolocation) {
        const storedValues = window.localStorage.userLong;
        if (!storedValues) {
          navigator.geolocation.getCurrentPosition((position) => {
            this.userLong = position.coords.longitude;
            this.userLat = position.coords.latitude;
            window.localStorage.userLat = this.userLat;
            window.localStorage.userLong = this.userLong;
            this.isLatLongSet = true;
          })
        }
      }
      this.userLocation = window.localStorage.userLocation;
      if(this.userLong || this.userLocation) {
        this.isLatLongSet = true;
      } else {
        this.isLatLongSet = false;
      }

    },
    watch: {
      isLatLongSet(val) {
        if(val) {
          if(this.userLocation) {
            return this.getWeatherData(this.userLocation)
            .then(response => {
              return this.setResponseData(response);
            })
          } else {
            return this.getWeatherData("",this.userLat,this.userLong)
            .then(response => {
              return this.setResponseData(response);
            })
          }
        }
      }
    },
    methods:{
      getWeatherByLocation() {
        window.localStorage.userLocation = this.userLocation;
        return this.getWeatherData(this.userLocation)
        .then(response => {
          return this.setResponseData(response);
        })
      },
      getWeatherData(location="",lat="",long="") {
        let url = "https://api.openweathermap.org/data/2.5/weather";
        let api = "b5f558462160da78810acd0bb997a9fd";
        let apiURL;
        if(location) {
          apiURL= url+"?q="+location+"&appid="+api;
        }
        if(lat && long) {
          apiURL = url+"?lat="+lat+"&lon="+long+"&appid="+api;
        }
        const config = {
          method: 'get',
          url: apiURL
        };
        return axios(config)
        .then(function (response) {
          return (response.data);
        })
        .catch(function (error) {
          console.warn(error);
        });
      },
      setResponseData(data) {
            this.direction = this.getDirection(data.wind.deg);
            this.wind = data.wind.speed;
            this.feel = Math.floor(data.main.feels_like - 273.15);
            this.humid = data.main.humidity;
            this.pressure = data.main.pressure;
            this.name = data.name;
            this.temp = Math.floor(data.main.temp - 273.15) +  "°C";
            this.description = "";
            this.marker = "";
            this.slash = "";
            this.max = Math.ceil(data.main.temp_min - 273.15) + "°C";
            this.min = Math.floor(data.main.temp_min - 273.15) + "°C";
            this.country = data.sys.country;
            this.desc = data.weather[0].main;
            this.img = data.weather[0].icon;

            this.isData = true;
            return;
      },
      getDirection(direction) {
        if (direction > 348.75 || direction <= 11.25) {
          direction = "N";
        } else if (direction > 11.25 && direction <= 33.75) {
          direction = "NNE";
        } else if (direction > 33.75 && direction <= 56.25) {
          direction = "NE";
        } else if (direction > 56.25 && direction <= 78.75) {
          direction = "ENE";
        } else if (direction > 78.75 && direction <= 101.25) {
          direction = "E";
        } else if (direction > 101.25 && direction <= 123.75) {
          direction = "ESE";
        } else if (direction > 123.75 && direction <= 146.25) {
          direction = "SE";
        } else if (direction > 146.25 && direction <= 168.75) {
          direction = "SSE";
        } else if (direction > 168.75 && direction <= 191.25) {
          direction = "S";
        } else if (direction > 191.25 && direction <= 213.75) {
          direction = "SSW";
        } else if (direction > 213.25 && direction <= 236.75) {
          direction = "SW";
        } else if (direction > 236.75 && direction <= 258.25) {
          direction = "WSW";
        } else if (direction > 258.25 && direction <= 281.75) {
          direction = "W";
        } else if (direction > 281.75 && direction <= 303.25) {
          direction = "WNW";
        } else if (direction > 303.25 && direction <= 326.75) {
          direction = "NW";
        } else if (direction > 326.75 && direction <= 348.75) {
          direction = "NNW";
        }
        return direction;
      }
    }
  }
</script>

<style>

h2 {
  font-weight: 900;
  text-transform: uppercase;
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
}

.mt-top {
  clear:both;
  margin-top: 45px;
  text-align: center;
  float: right;
}
.mt-top-v1 {
  clear:both;
  margin-top: 45px;
  display: flex;
  place-content: center;
}
.inputValue  {
  position: relative;
  width: 250px;
  padding: 8px;
  margin: 0px;
  background-color: rgb(243 241 241);
  border: 0;
  border-radius: 5px;
}
.searchBtn {
    outline: none;
    background: black;
    color: white !important;
    margin: 5px;
    padding: 8px;
    border: 0;
    border-radius: 5px;
}
.skeleton-top {
  margin-top: 100px;
  text-align: -webkit-center;
}
</style>