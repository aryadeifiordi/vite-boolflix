<template>
  <div id="app">
    <MovieSearch />
  </div>
</template>

<script setup>
import MovieSearch from './components/MovieSearch.vue'
</script>

<style>
body {
  margin: 0;
  padding: 0;
  overflow: hidden;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: black;
}

#app {
  width: 100%;
  height: 100vh;

}
</style>
