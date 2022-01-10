<template>
  <h1>Chart Types</h1>

  <ul>
    <li v-for="(item, index) in chartTypes" :key="index">
      <a :href="`#${item}`">{{ item }}</a>
    </li>
  </ul>
</template>

<script setup>
const chartTypes = [
  "line",
  "bar",
  "radar"
  // "doughnut",
  // "pie",
  // "polar-area",
  // "bubble",
  // "scatter",
  // "area",
  // "mixed"
]
</script>

<style scoped>
ul {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
}
</style>
