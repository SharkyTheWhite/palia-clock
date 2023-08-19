<script setup lang="ts">
import SvgDonutArc from "./SvgDonutArc.vue";
import { computed, onMounted, onUnmounted, ref } from 'vue';

const realTimeOfDay = ref<number>(0); // in Minutes since midnight

function updateRealTime() {
  const now = new Date();
  const epoch_seconds = now.getTime() / 1000;
  const epoch_minutes = epoch_seconds / 60;
  realTimeOfDay.value = epoch_minutes % (60 * 24);
}

let todInterval: number | null = null;

onMounted(() => {
  updateRealTime();
  todInterval = setInterval(updateRealTime, 250);
});

onUnmounted(() => {
  if (todInterval) clearInterval(todInterval);
  todInterval = null;
});

const debugSpeedup = 1;

const palianTimeOfDay = computed(() => {
  // One Palian day equals one real-life hour and starts on the full hour.
  return (realTimeOfDay.value * debugSpeedup * 24) % (60 * 24);
});

function d2(x: number): string {
  return x.toFixed(0).padStart(2, '0');
}

const clockText = computed(() => {
  const tod = Math.floor(palianTimeOfDay.value);
  const m = tod % 60;
  const h = (tod - m) / 60;
  return `${d2(h)}:${d2(m)}`;
});

const partOfDay = computed(() => {
  const h = palianTimeOfDay.value / 60;
  if (h >= 21 || h < 3) return 'Night'; // 21 to 3
  if (h >= 18) return 'Evening'; // 18 to 21
  if (h >= 6) return 'Day'; // 6 to 18
  return 'Morning'; // 3 to 6
});

const dialTransform = computed(() => {
  // 360 degrees per palian day
  const degrees = (360 * palianTimeOfDay.value) / (60 * 24);
  // Midnight is bottom of dial
  return `rotate(${degrees + 90})`;
});

const ri = 25;
const ra = 43;
</script>

<template>
  <svg width="500" height="500" viewBox="0 0 500 500" xmlns="http://www.w3.org/2000/svg">
    <g transform="scale(5) translate(50,50)">
      <circle class="clock" r="45" cx="0" cy="0" />
      <SvgDonutArc class="segment" a0="0" a1="45" :ri="ri" :ra="ra" fill="darkorange" />
      <SvgDonutArc class="segment" a0="45" a1="135" :ri="ri" :ra="ra" fill="navy" />
      <SvgDonutArc class="segment" a0="135" a1="180" :ri="ri" :ra="ra" fill="yellow" />
      <SvgDonutArc class="segment" a0="180" a1="0" :ri="ri" :ra="ra" fill="lightblue" />
      <path class="dial" :d="`M 35 0 L 44 -8 A 43 43 0 0 1 44 8 z`" :transform="dialTransform" />
      <text y="6" font-size="14">{{ clockText }}</text>
      <text y="-7.5" font-size="8">{{ partOfDay }}</text>
      <!-- <text y="12.5" font-size="6">Palian Time</text> -->
    </g>
  </svg>
</template>

<style scoped>
.clock {
  fill: gray;
  stroke: black;
  stroke-width: 1px;
}
.dial {
  fill: black;
  stroke: none;
}
.segment {
  stroke: gray;
  stroke-width: 1px;
}
text {
  text-anchor: middle;
  font-weight: bold;
  fill: black;
}
</style>
