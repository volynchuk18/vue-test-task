<template>
  <div id="app">
    <input
      @input="mapboxPlacesRequestHandler"
      v-model="coordinationsInput"
    >
    <div v-if="mapboxPlacesResponse">
      {{getTextArrayFromResponse}}
    </div>
    <input
      v-model="locationInput"
    >
    <div>{{similarDataEnoughtLength}}</div>
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
    }
  },
  methods: {
    mapboxPlacesRequestHandler() {
      if (!this.coordinationsInput) {
        return
      }
      const data = {
        type: 'address',
        access_token: 'pk.eyJ1IjoiYWtpdm1vb3ZleCIsImEiOiJjanVndGYzaW8wbnc3NDl0OWQ5dGhnMXFmIn0.FmA74joXKHPcNrPFb4B6nA'
      };
      try {
        axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${this.coordinationsInput}.json`, {params: {...data}})
          .then( res => {
            this.mapboxPlacesResponse = res.data.features
          })
      } catch (e) {
        console.error(e)
      }
    },
  },
  computed: {
    splitedLocationInput() {
      return this.locationInput && this.locationInput.split(',').map(item => item.trim())
    },
    getTextArrayFromResponse() {
      return this.mapboxPlacesResponse && this.mapboxPlacesResponse.map(item => item.text)
    },
    compareInputWithResponse() {
      if (!this.getTextArrayFromResponse || !this.splitedLocationInput) {
        return
      } else {
        return this.getTextArrayFromResponse.filter(value => this.splitedLocationInput.includes(value))
      }
    },
    similarDataEnoughtLength() {
      return this.compareInputWithResponse && this.compareInputWithResponse.length > (this.getTextArrayFromResponse.length / 2)
    }
  },
}
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
  width: 30%;
  margin: 0 auto;
  justify-content: center;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
