<template>
  <div id="app">
    <CoordPickerup />
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import CoordPickerup from './components/CoordPickeup'

export default {
  name: 'App',
  components: {
    // HelloWorld
    CoordPickerup
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
