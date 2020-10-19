<template>
  <div id="app">
    <DailySalesSchedule></DailySalesSchedule>
  </div>
</template>

<script>
import DailySalesSchedule from './components/DailySalesSchedule'
export default {
  name: 'App',
  components: {
    DailySalesSchedule
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
