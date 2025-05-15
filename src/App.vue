<template>
  <div class="container">
    <HomeView />
  </div>
</template>

<script setup>
import HomeView from "./views/HomeView.vue";
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
.container {
  max-width: 1440px;
  padding: 0 20px;
  margin-inline: auto;
}
</style>
