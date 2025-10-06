<script setup>
import Thermometer from './components/Thermometer.vue'
import OshwaLogo from './assets/oshwa_logo.svg?component'
</script>

<template>
  <div>
    <a href="https://oshwa.org/" target="_blank">
      <OshwaLogo class="logo"/>
    </a>
  </div>
  <div class="card-container">
    <Thermometer title="Membership" unit=" New Members" :min="0" :max="100"/>
    <Thermometer title="Donations" unit=" $" :min="0" :max="2000"/>
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em hsla(202, 100%, 49%, 0.886));
}
</style>
