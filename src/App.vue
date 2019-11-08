<template>
  <div id="app">
    <CryptoList/>
  </div>
</template>

<script>
import CryptoList from './components/CryptoList.vue'

export default {
  name: 'app',
  components: {
    CryptoList
  }
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
