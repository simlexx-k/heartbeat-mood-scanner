<template>
  <div id="app">
    <HeartbeatMoodDetector />
  </div>
</template>

<script>
import HeartbeatMoodDetector from './components/HeartbeatMoodDetector.vue'

export default {
  name: 'App',
  components: {
    HeartbeatMoodDetector
  }
}
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
