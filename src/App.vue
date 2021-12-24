<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <contacts :map-data="contactMap"></contacts>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import contactMap from "./mock.js"
import contacts from "./components/contacts"
export default {
  name: 'App',
  components: {
    // HelloWorld
    contacts
  },
  data(){
    return {
      contactMap
    }
  }
}
</script>

<style lang="scss">
*{
  margin:0;
  padding:0;
  box-sizing: border-box;
}
html,body{
  height: 100%;
}
ul,li{
        margin:0;
        padding:0;
        list-style: none;
    }
#app {
  height: 100%;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
