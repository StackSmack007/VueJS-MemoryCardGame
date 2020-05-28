<template>
  <div id="app">
    <CardGame/>
  </div>
</template>

<script>
import CardGame from './components/CardGame.vue'

export default {
  name: 'App',
  components: {
    CardGame
  }
}
</script>

<style>
@import url("./styles/reset.css");
@import url("./styles/layout.css");
@import url("./styles/typography.css");

:root {
    --primary: rgb(167, 49, 167) /* accessible everywhere */
}

#app {
  height:100vh; 
  padding:2em;
  background-image: linear-gradient(to right,rgb(211, 252, 122),rgb(158, 233, 157));
  /* -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px; */
}
</style>
