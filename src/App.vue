<template>
  <div id="app">
    <Track />
    <!-- <CoordPickerup /> -->
  </div>
</template>

<script>
// import CoordPickerup from './components/CoordPickeup'
import Track from './components/Track'

export default {
  name: 'App',
  components: {
    // CoordPickerup
    Track
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
