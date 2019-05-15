<template>
  <div id="app">
    <input
      class="location-input"
      placeholder="Input coordinates, ex: -96.5738,39.1748"
      v-model="coordinationsInput"
    >
    <input
      class="location-input"
      v-model="locationInput"
      placeholder="Input location, ex: United States, Kansas, Manhattan,66502"
    >
    <div
      class="location__correct"
      :style="{background: similarDataEnoughtLength ? 'green' : 'red'}"
      v-html="validText"
    >
    </div>
    <div class="map-container">
      <div id="map"></div>
      <div id="coordinates" class="coordinates"></div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'app',
  data() {
    return {
      mapboxPlacesResponse: null,
      coordinationsInput: null,
      locationInput: null,
      accessToken: 'pk.eyJ1IjoiYWtpdm1vb3ZleCIsImEiOiJjanVndGYzaW8wbnc3NDl0OWQ5dGhnMXFmIn0.FmA74joXKHPcNrPFb4B6nA',
    }
  },
  watch: {
    coordinationsInput() {
      this.mapboxPlacesRequestHandler()
    }
  },
  mounted() {
    let mapboxgl = require('mapbox-gl/dist/mapbox-gl.js');
    mapboxgl.accessToken = this.accessToken;
    let coordinates = document.getElementById('coordinates');
    let map = new mapboxgl.Map({
      container: 'map',
      style: 'mapbox://styles/mapbox/streets-v11',
      center: [0, 0],
      zoom: 2
    });

    let marker = new mapboxgl.Marker({
      draggable: true
    })
      .setLngLat([0, 0])
      .addTo(map);

    const onDragEnd = () => {
      let lngLat = marker.getLngLat();
      coordinates.style.display = 'block';
      coordinates.innerHTML = 'Longitude: ' + lngLat.lng + '<br />Latitude: ' + lngLat.lat;
      this.coordinationsInput = lngLat.lng + ',' + lngLat.lat;
      console.log()
    }

    marker.on('dragend', onDragEnd);
  },
  methods: {
    mapboxPlacesRequestHandler() {
      if (!this.coordinationsInput) {
        return
      }
      const data = {
        type: 'address',
        access_token: this.accessToken,
      };
      try {
        axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${this.coordinationsInput.trim()}.json`, {params: {...data}})
          .then( res => {
            this.mapboxPlacesResponse = res.data.features
          })
      } catch (e) {
        console.error(e)
      }
    },
  },
  computed: {
    validText() {
      return this.similarDataEnoughtLength ? 'Valid' : 'Not valid';
    },
    splitedLocationInput() {
      return this.locationInput && this.locationInput.split(',').map(item => item.trim());
    },
    getTextArrayFromResponse() {
      return this.mapboxPlacesResponse && this.mapboxPlacesResponse.map(item => item.text);
    },
    compareInputWithResponse() {
      if (!this.getTextArrayFromResponse || !this.splitedLocationInput) {
        return
      } else {
        return this.getTextArrayFromResponse.filter(value => this.splitedLocationInput.includes(value));
      }
    },
    similarDataEnoughtLength() {
      return this.compareInputWithResponse && this.compareInputWithResponse.length >= (this.getTextArrayFromResponse.length / 2);
    }
  },
}
</script>

<style scoped>
  .map-container {
    margin: auto;
    position: relative;
    width: 70%;
    height: 50vh;
  }

  .coordinates {
    background: rgba(0,0,0,0.5);
    color: #fff;
    position: absolute;
    bottom: 0;
    left: 10px;
    padding:5px 10px;
    margin: 0;
    font-size: 11px;
    line-height: 18px;
    border-radius: 3px;
    display: none;
  }

  .location__correct {
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .location-input {
    width: 30%;
    margin: 15px auto 0 auto;
    height: 30px;
    font-size: 22px;
    border-radius: 6px;
  }

  .location__correct {
    height: 100px;
    border-radius: 50%;
    width: 150px;
    margin: 0 auto;
    margin-top: 50px;
  }

  #app {
    display: flex;
    flex-direction: column;
    margin: 0 auto;
    justify-content: center;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  #map {
    width: 100%;
    height: 100%;
    display: flex;
    margin: 50px auto 0 auto;
  }
</style>
