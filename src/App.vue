<template>
  <div id="app">
    <div id="index">
      <img alt="Vue logo" src="./assets/logo.png">
    </div>
    
    <div>
      <FrontEnd msg="Welcome to Your Vue.js App"/>
    </div>
    
  </div>
</template>

<script>
import FrontEnd from './components/FrontEnd'

export default {
  name: 'App',
  components: {
    FrontEnd
  }
}
</script>

<style>
#app {
  
}
#index {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
