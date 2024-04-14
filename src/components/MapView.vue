<script setup>
import { createApp, h, onMounted } from 'vue'
import { Loader } from "@googlemaps/js-api-loader"
import { fetchWeatherApi } from 'openmeteo';
import Clock from "./Clock.vue"

const API_KEY = import.meta.env.VITE_API_KEY
const URL_WEATHER = import.meta.env.VITE_URL_WEATHER
const URL_GET_TIMEZONE = import.meta.env.VITE_URL_GET_TIMEZONE

async function handleClickMap(geocoder, map, infowindow, latlng, marker) {
  infowindow.setPosition(latlng);
  infowindow.open(map);
  infowindow.setContent("<img width='20' src='https://i.stack.imgur.com/kOnzy.gif'/>")
  marker.setPosition(latlng)
  const params = {
    latitude: latlng.lat(),
    longitude: latlng.lng(),
    current: "temperature_2m",
  };
  const timezone = await getAndShowTimezone(latlng.lat(), latlng.lng())
  const urlApiWeather = `${URL_WEATHER}/forecast`;
  const responses = await fetchWeatherApi(urlApiWeather, params);

  const response = responses[0];

  const utcOffsetSeconds = response.utcOffsetSeconds();
  const current = response.current();
  const weatherData = {
    current: {
      time: new Date((Number(current.time()) + utcOffsetSeconds) * 1000),
      temperature2m: current.variables(0).value(),
    },
  };

  geocoder.geocode({ 'location': latlng }, function (results, status) {
    if (status === 'OK') {
      if (results[0]) {
        const clockComponent = createApp({
          render: () => h(Clock, { address: results[0].formatted_address, weather: Math.ceil(weatherData.current.temperature2m), timezone })
        })
        const mountPoint = document.createElement('div')
        clockComponent.mount(mountPoint)
        infowindow.addListener('closeclick', () => {
          clockComponent.unmount();
        });
        infowindow.setContent(mountPoint)
      } else {
        window.alert('No results found');
      }
    } else {
      window.alert('Geocoder failed due to: ' + status);
    }
  });
}

function getAndShowTimezone(lat, lng) {
  return new Promise((resolve, reject) => {
    try {
      const timestamp = Math.floor(Date.now() / 1000);
      const apiUrl = `${URL_GET_TIMEZONE}?location=${lat},${lng}&timestamp=${timestamp}&key=${API_KEY}`;
      fetch(apiUrl)
        .then(response => response.json())
        .then(data => {
          resolve(data.timeZoneId)
        });
    } catch (error) {
      reject(error)
    }
  });
}

onMounted(() => {
  const loader = new Loader({
    apiKey: API_KEY,
    version: "weekly",
  });

  loader.load().then(async (google) => {
    navigator.geolocation.getCurrentPosition((position) => {
      const pos = {
        lat: position.coords.latitude,
        lng: position.coords.longitude,
      };

      const map = new google.maps.Map(
        document.getElementById("map"),
        {
          center: pos,
          zoom: 14,
        }
      );

      const maker = new google.maps.Marker({
        position: pos,
        map: map,
      });

      const infoWindow = new google.maps.InfoWindow();
      const geocoder = new google.maps.Geocoder;

      map.addListener('click', function (event) {
        handleClickMap(geocoder, map, infoWindow, event.latLng, maker);
      });
    }, (err) => {
      console.log("🚀 ~ err:", err)
    });
  });
})
</script>

<template>
  <div class="card">
    <div id="map" />
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}

#map {
  height: 800px;
  width: 100%;
}
</style>
