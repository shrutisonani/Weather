<template>
  <div>
    <!-- search field -->
    <form>
      <div class="bg-white border border-indigo-600/30 rounded-lg shadow-lg flex items-center">
        <i class="fa-solid fa-magnifying-glass p-2 text-indigo-600"></i>
        <input
          type="text"
          placeholder="Search for a place"
          class="rounded-r-lg p-2 border-0 outline-0 focus:ring-2 focus:ring-indigo-600 ring-inset w-full"
          v-model="searchTerm.query"
          @input="handleSearch"
        />
      </div>
    </form>
    <!-- search suggestions -->
    <div class="bg-white my-2 rounded-lg shadow-lg">
      <div v-if="searchTerm.results !== null">
        <div v-for="place in searchTerm.results" :key="place.id">
          <button
            class="px-3 my-2 hover:text-indigo-600 hover:font-bold w-full text-left"
            @click="getWeather(place.id)"
          >
            {{ place.name }}, {{ place.region }}, {{ place.country }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive } from 'vue'

// emit the searching weather option
const emit = defineEmits(['place-data'])

const searchTerm = reactive({
  query: '',
  timeOut: null,
  results: ''
})

// handle the searching function
const handleSearch = () => {
  clearTimeout(searchTerm.timeOut)
  searchTerm.timeOut = setTimeout(async () => {
    if (searchTerm.query !== '') {
      const res = await fetch(
        `http://api.weatherapi.com/v1/search.json?key=2970684bca524c2dbfa113006240205&q=${searchTerm.query}`
      )

      const data = await res.json()
      searchTerm.results = data
    } else {
      searchTerm.results = null
    }
  }, 500)
}

// get the searching weather option
const getWeather = async (id) => {
  const res = await fetch(
    `http://api.weatherapi.com/v1/forecast.json?key=2970684bca524c2dbfa113006240205&q=id:${id}&days=3&aqi=no&alerts=no`
  )
  const data = await res.json()

  // emit the data
  emit('place-data', data)

  searchTerm.query = ''
  searchTerm.results = null
}
</script>
