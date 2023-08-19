<template>
  <path :d="path_d" />
</template>

<script setup lang="ts">
import { computed } from 'vue';

const props = defineProps<{ a0: number; a1: number; ri: number; ra: number }>();

const path_d = computed(() => {
  const a0r = (Math.PI * props.a0) / 180.0;
  const a1r = (Math.PI * props.a1) / 180.0;
  const x0 = props.ri * Math.cos(a0r);
  const y0 = props.ri * Math.sin(a0r);
  const x1 = props.ra * Math.cos(a0r);
  const y1 = props.ra * Math.sin(a0r);
  const x2 = props.ra * Math.cos(a1r);
  const y2 = props.ra * Math.sin(a1r);
  const x3 = props.ri * Math.cos(a1r);
  const y3 = props.ri * Math.sin(a1r);
  return `M ${x0} ${y0} L ${x1} ${y1}A ${props.ra} ${props.ra} 0 0 1 ${x2} ${y2} L ${x3} ${y3} A ${props.ri} ${props.ri} 0 0 0 ${x0} ${y0}`;
});
</script>
