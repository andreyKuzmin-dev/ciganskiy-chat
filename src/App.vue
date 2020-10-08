<template>
  <div id="app">
    <main-container></main-container>
  </div>
</template>

<script>

import MainContainer from "./components/main-container";
export default {
  name: 'App',
  components: {MainContainer}
}
</script>

<style>
  * {
    margin: 0;
  }
  body {
    background-color: #2b2b2b;
  }
  #app {
    font-family: "Roboto", Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #fafafa;
  }
  .pointer:hover {
    cursor: pointer;
  }
  .push:active {
    transform: scale(0.99);
  }
</style>
