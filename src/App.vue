<template>
  <div v-if="error">{{ error }}</div>

  <Suspense>
    <template #default>
      <LineChart />
    </template>

    <template #fallback>Loading your line chart...</template>
  </Suspense>
  <!-- <BarChart />
    <RadarChart />
    <DoughnutChart />
    <PieChart />
    <PolarAreaChart />
    <BubbleChart />
    <ScatterChart />
    <AreaChart />
  <MixedChart />-->
</template>

<script setup>
import { ref, onErrorCaptured } from "vue"
import LineChart from "./components/LineChart.vue"
// import BarChart from "./components/BarChart.vue"
// import RadarChart from "./components/RadarChart.vue"
// import DoughnutChart from "./components/DoughnutChart.vue"
// import PieChart from "./components/PieChart.vue"
// import PolarAreaChart from "./components/PolarAreaChart.vue"
// import BubbleChart from "./components/BubbleChart.vue"
// import ScatterChart from "./components/ScatterChart.vue"
// import AreaChart from "./components/AreaChart.vue"
// import MixedChart from "./components/MixedChart.vue"

import { Chart, Legend, Title, Tooltip } from "chart.js"

Chart.register(Legend, Title, Tooltip)

Chart.defaults.color = "#363636"
Chart.defaults.borderColor = "#dbdbdb"
Chart.defaults.font.size = 16
Chart.defaults.font.lineHeight = 1.5
Chart.defaults.layout.padding = 32
Chart.defaults.plugins.legend.position = "bottom"
Chart.defaults.plugins.legend.padding = 16
Chart.defaults.plugins.title.display = true
Chart.defaults.plugins.title.padding = 16

const error = ref(null)

onErrorCaptured((e) => (error.value = e))
</script>

<style>
.chart-container {
  border: 0.25rem solid var(--background);
  border-radius: 0.5rem;
}
</style>
