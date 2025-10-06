<script setup>
import Thermometer from './components/Thermometer.vue'
</script>

<template>
  <div>
    <a href="https://oshwa.org/" target="_blank">
      <img src="/oshwa_logo.svg" class="logo" alt="OSHWA logo" />
    </a>
  </div>
  <Thermometer msg="0%" />
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #64ffffaa);
}
</style>
