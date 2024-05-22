<template>
  <FreeSlotMatchComponent msg="Free Slot Matching"/>
</template>

<script>
import FreeSlotMatchComponent from './components/FreeSlotMatchComponent.vue'

export default {
  name: 'App',
  components: {
    FreeSlotMatchComponent
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
