<template>
  <h1>Line Chart</h1>

  <LineChart
    :chart-data="data"
    :options="options"
    css-classes="chart-container"
  />

  <div>
    <select
      v-for="n in 3"
      v-model="selected[`country${n}`]"
      @change="getData(n)"
    >
      <option disabled value>Country {{ n }}</option>
      <option v-for="item in countries" :key="item" :value="item">
        {{ item }}
      </option>
    </select>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue"
import axios from "axios"

// chart imports and register
import { LineChart } from "vue-chart-3"
import {
  Chart,
  LineController,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement
} from "chart.js"

Chart.register(
  LineController,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement
)

// api
const headers = {
  "x-rapidapi-host": "covid-193.p.rapidapi.com",
  "x-rapidapi-key": import.meta.env.VITE_RAPIDAPI_KEY
}

// v-model
const selected = ref({
  country1: "",
  country2: "",
  country3: ""
})

// list of countries
const countries = ref([])

// send request to get the list
const getCountries = async () => {
  countries.value = await axios
    .request({
      method: "GET",
      url: "https://covid-193.p.rapidapi.com/countries",
      headers
    })
    .then(function (response) {
      return response.data.response
    })
    .catch(function (error) {
      console.error(error)
    })
}

// fill the select options on mount
onMounted(getCountries)

// data for each country
const data1 = ref([]),
  data2 = ref([]),
  data3 = ref([])

// compute labels
const dates1 = computed(() => {
    return data1.value.map((item) => item.day)
  }),
  dates2 = computed(() => {
    return data2.value.map((item) => item.day)
  }),
  dates3 = computed(() => {
    return data3.value.map((item) => item.day)
  }),
  labels = computed(() => {
    const all = dates1.value.concat(dates2.value).concat(dates3.value)

    // return unique values
    return [...new Set(all)].sort()
  })

// send data request for a country
function getHistory(country) {
  return axios
    .request({
      method: "GET",
      url: "https://covid-193.p.rapidapi.com/history",
      params: { country: country.toLowerCase() },
      headers
    })
    .then((response) => {
      // remove duplicate days
      return [
        ...new Map(
          response.data.response
            .slice()
            .reverse()
            .map((v) => [v.day, v])
        ).values()
      ]
    })
    .catch(function (error) {
      console.error(error)
    })
}

// send data request for changed input then update data value
async function getData(n) {
  switch (n) {
    case 1:
      data1.value = await getHistory(selected.value.country1)
      break
    case 2:
      data2.value = await getHistory(selected.value.country2)
      break
    case 3:
      data3.value = await getHistory(selected.value.country3)
      break
  }
}

// chart data
const data = computed(() => ({
  labels: labels.value,

  datasets: [
    {
      label: selected.value.country1,
      data: data1.value,
      borderColor: "#268bd2",
      backgroundColor: "#268bd2"
    },
    {
      label: selected.value.country2,
      data: data2.value,
      borderColor: "#2aa198",
      backgroundColor: "#2aa198"
    },
    {
      label: selected.value.country3,
      data: data3.value,
      borderColor: "#859900",
      backgroundColor: "#859900"
    }
  ]
}))

// chart options
const options = ref({
  parsing: {
    xAxisKey: "day",
    yAxisKey: "cases.active"
  },
  scales: {
    y: {
      beginAtZero: true,
      min: 0,
      ticks: {
        precision: 0
      }
    }
  },
  plugins: {
    title: {
      text: "COVID-19 Active Cases Between Countries"
    }
  }
})
</script>

<style scoped>
div {
  display: flex;
  justify-content: space-between;
  margin-top: 2rem;
}

select {
  margin: 0;
}
</style>
