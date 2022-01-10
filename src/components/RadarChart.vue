<template>
  <h2 id="radar">Radar Chart</h2>

  <RadarChart
    :chart-data="data"
    :options="options"
    css-classes="chart-container"
  />

  <div>
    <select
      v-for="n in 2"
      v-model="selected[`pokemon${n}`]"
      @change="updateData(n)"
    >
      <option disabled value>Pokemon {{ n }}</option>
      <option v-for="(item, index) in pokemonNames" :key="index" :value="item">
        {{ item }}
      </option>
    </select>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue"
import axios from "axios"

// chart imports and register
import { RadarChart } from "vue-chart-3"
import {
  Chart,
  RadarController,
  RadialLinearScale,
  PointElement,
  LineElement
} from "chart.js"

Chart.register(RadarController, RadialLinearScale, PointElement, LineElement)

// v-model
const selected = ref({
  pokemon1: "",
  pokemon2: ""
})

const pokemonStats = ref([])

// send request to get the stats
const getStats = async () => {
  pokemonStats.value = await axios
    .request({
      method: "GET",
      url: "https://pokemon-go1.p.rapidapi.com/pokemon_stats.json",
      headers: {
        "x-rapidapi-host": "pokemon-go1.p.rapidapi.com",
        "x-rapidapi-key": import.meta.env.VITE_RAPIDAPI_KEY
      }
    })
    .then(function (response) {
      // remove duplicate names
      return [
        ...new Map(
          response.data
            .slice()
            .reverse()
            .map((v) => [v.pokemon_name, v])
        ).values()
      ]
    })
    .catch(function (error) {
      console.error(error)
    })
}

// fill the select options on mount
onMounted(getStats)

// data for each pokemon
const data1 = ref([]),
  data2 = ref([])

// compute names
const pokemonNames = computed(() => {
  return pokemonStats.value.map((item) => item.pokemon_name).sort()
})

function getData(name) {
  const obj = pokemonStats.value.filter((item) => item.pokemon_name === name)[0]

  return [obj.base_attack, obj.base_defense, obj.base_stamina]
}

function updateData(n) {
  switch (n) {
    case 1:
      data1.value = getData(selected.value.pokemon1)
      break
    case 2:
      data2.value = getData(selected.value.pokemon2)
      break
  }
}

// chart data
const data = computed(() => ({
  labels: ["Attack", "Defense", "Stamina"],

  datasets: [
    {
      label: selected.value.pokemon1,
      data: data1.value,
      borderColor: "#268bd2",
      pointBackgroundColor: "#268bd2",
    },
    {
      label: selected.value.pokemon2,
      data: data2.value,
      borderColor: "#2aa198",
      pointBackgroundColor: "#2aa198"
    }
  ]
}))

// chart options
const options = ref({
  scales: {
    r: {
      min: 0,
      ticks: {
        precision: 0
      }
    }
  },
  plugins: {
    title: {
      text: "Pokemon Base Stats"
    }
  }
})
</script>

<style scoped>
div {
  display: flex;
  margin-top: 2rem;
}

select {
  margin: 0;
}

select:not(:first-child) {
  margin-left: 3rem;
}
</style>
