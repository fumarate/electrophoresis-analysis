<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <EAnalyzer></EAnalyzer>
  </div>
</template>

<script>
import EAnalyzer from './components/EAnalyzer.vue'

export default {
  name: 'App',
  components: {
    EAnalyzer
  }
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
