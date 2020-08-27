<template>
  <div id="app">
    <h1>Vue photos</h1>
    <div v-bind:key="photo.id" v-for="photo in photos">
      <img :src="photo.url" />
    </div>
    <audio autoplay></audio>
  </div>
</template>

<script>
import axios from 'axios'

const constraints = {
  audio: true,
}

const chunks = []

const audio = document.querySelector('audio')

navigator.mediaDevices.getUserMedia(constraints).then((stream) => {
  audio.srcObject = stream
})

export default {
  name: 'App',
  components: {},
  data() {
    return {
      photos: [],
    }
  },
  created() {
    axios
      .get('https://jsonplaceholder.typicode.com/photos?_limit=10')
      .then((res) => (this.photos = res.data))
      .catch((err) => console.log(err))
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
