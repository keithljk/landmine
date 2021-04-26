<template>
  <div id="app">
    <Mini/>
  </div>
</template>

<script>
import Mini from './components/mini.vue'

export default {
  name: 'App',
  components: {
    Mini
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
  margin: 0 auto;
  width: 25%;
}
</style>
