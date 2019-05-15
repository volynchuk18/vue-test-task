<template>
  <div id="app">
    <input @input="request" v-model="input">
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'app',
  methods: {
    request() {
      const data = {
        type: 'address',
        access_token: 'pk.eyJ1IjoiYWtpdm1vb3ZleCIsImEiOiJjanVndGYzaW8wbnc3NDl0OWQ5dGhnMXFmIn0.FmA74joXKHPcNrPFb4B6nA'
      };
      axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${this.input}.json`, {params: {...data}})
    }
  },
  data() {
    return {
      input: null,
    }
  },
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
