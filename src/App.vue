<script setup lang="ts">
import PaliaClock from './components/PaliaClock.vue';
</script>

<template>
  <h1>Palia Clock</h1>
  <PaliaClock class="palia-clock" />
  <p>
    Assumption: Palia week begins on Sundays 9 PM PST (UTC-8), which will show
    as "Day 1 Cycle 1". Each Day has 24 Cycles, the Week has 7 Days.
  </p>
  <p>
    This is an experimental little tool for players of the online game Palia.<br />
    <a href="https://github.com/SharkyTheWhite/palia-clock">
      About this Project (includes referral code 😇) </a
    ><br />
    <em>
      This project is neither affiliated with nor officially supported by the
      creators of Palia.
    </em>
  </p>
</template>

<style scoped>
.palia-clock {
  max-width: 85vw;
  max-height: 85vw;
}
</style>
