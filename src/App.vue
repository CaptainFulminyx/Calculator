<template>
  <div id="app">
    <DoodlePortfolio />
  </div>
</template>

<script setup>
import DoodlePortfolio from './components/Site.vue'
</script>

<style>
/* Import Font Awesome and Google Fonts */
@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css');
@import url('https://fonts.googleapis.com/css2?family=Indie+Flower&family=Poppins:wght@300;400;500;600;700&display=swap');

/* Reset and base styles */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 0;
  min-height: 100vh;
}
</style>