<script setup lang="ts">
import SvgDonutArc from './SvgDonutArc.vue';
import { computed, onMounted, onUnmounted, ref, watch } from 'vue';

const realTimePST = ref<number>(0); // in Seconds since Sunday, 3.1.1970 PST

// Subtract this from UTC timestamp to get seconds since Sunday, 3.1.1970 PST
// PST is UTC-8 so we subtract 8 hours and 3 days
const PST_UTC_SUNDAY_OFFSET = 60 * 60 * (8 + 3 * 24);
const MINUTE = 60;
const HOUR = 60 * 60;
const DAY = 60 * 60 * 24;

function updateRealTime() {
  const now = new Date();
  const epoch_seconds = now.getTime() / 1000;
  realTimePST.value = epoch_seconds - PST_UTC_SUNDAY_OFFSET;
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

const palianTimeOfDay = computed(() => {
  // One Palian day equals one real-life hour and starts on the full hour.
  return (realTimePST.value * 24) % DAY;
});

function d2(x: number): string {
  return x.toFixed(0).padStart(2, '0');
}

const clockText = computed(() => {
  const palianTimeOfDayMinutes = Math.floor(palianTimeOfDay.value / MINUTE);
  const m = palianTimeOfDayMinutes % 60;
  const h = (palianTimeOfDayMinutes - m) / 60;
  return `${d2(h)}:${d2(m)}`;
});

watch(clockText, (newClockText) => {
  document.title = `${newClockText} - Palia Clock`;
});

const partOfDay = computed(() => {
  const h = palianTimeOfDay.value / HOUR;
  if (h >= 21 || h < 3) return 'Night'; // 21 to 3
  if (h >= 18) return 'Evening'; // 18 to 21
  if (h >= 6) return 'Day'; // 6 to 18
  return 'Morning'; // 3 to 6
});

const dialTransform = computed(() => {
  // 360 degrees per palian day
  const degrees = (360 * palianTimeOfDay.value) / DAY;
  // Midnight is bottom of dial
  return `rotate(${degrees + 90})`;
});

/*
 * IRL Week starts Sunday 10 PM in PST zone (https://palia.wiki.gg/wiki/Hassian)?
 * IRL Day starts at 9 PM PST (10 PM PDT ?) (https://palia.wiki.gg/wiki/Gifting)?
 * PST is UTC-8 and has DST (PDT) but we assume it might not be relevant for Palia?
 * Since 9 and 10 PM are mentioned assuming 9PM is PST and 10pm is PDT.
 */
const timeSincePSTWeek = computed(() => {
  return (realTimePST.value - 21 * HOUR) % (7 * DAY);
});

const cycleText = computed(() => {
  const palianCycleThisWeek = timeSincePSTWeek.value / HOUR;
  const palianCycleThisDay = Math.floor(palianCycleThisWeek) % 24;
  const palianDayThisWeek = Math.floor(palianCycleThisWeek / 24);

  return `Day ${palianDayThisWeek + 1} Cycle ${d2(palianCycleThisDay + 1)}`;
});

const ri = 25;
const ra = 43;
</script>

<template>
  <svg
    width="500"
    height="500"
    viewBox="0 0 500 500"
    xmlns="http://www.w3.org/2000/svg">
    <g transform="scale(5) translate(50,50)">
      <circle class="clock" r="45" cx="0" cy="0" />
      <SvgDonutArc
        class="segment"
        :a0="0"
        :a1="45"
        :ri="ri"
        :ra="ra"
        fill="darkorange" />
      <SvgDonutArc
        class="segment"
        :a0="45"
        :a1="135"
        :ri="ri"
        :ra="ra"
        fill="navy" />
      <SvgDonutArc
        class="segment"
        :a0="135"
        :a1="180"
        :ri="ri"
        :ra="ra"
        fill="gold" />
      <SvgDonutArc
        class="segment"
        :a0="180"
        :a1="0"
        :ri="ri"
        :ra="ra"
        fill="lightblue" />
      <path
        class="dial"
        :d="`M 35 0 L 44 -8 A 43 43 0 0 1 44 8 z`"
        :transform="dialTransform" />
      <text y="6" font-size="14">{{ clockText }}</text>
      <text y="-7.5" font-size="8">{{ partOfDay }}</text>
      <text y="12.5" font-size="5">{{ cycleText }}</text>
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
