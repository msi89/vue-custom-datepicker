<template>
  <div id="app">
    <Datepicker value="2020-08-04" />
  </div>
</template>

<script>
import Datepicker from './components/Datepicker.vue'

export default {
  name: 'App',
  components: {
    Datepicker
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
