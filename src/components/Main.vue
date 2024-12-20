<script setup lang="ts">
import {ref, onMounted } from 'vue';
import SearchModal from './SearchModal.vue';
import WeatherTableRow from './Rows/WeatherTableRow.vue';
const cityName = 'kaunas'
var locations = ref([['','']])
var updateCount = ref(0)
var autoUpdateCount = ref(0)
var isSearchModalVisable = ref(false)
onMounted(() => {
  console.log(updateCount.value)
    if(updateCount.value===0){
      console.log("Updated on mount")
      updateTable()
      setInterval(autoUpdater, 300000);
    }
})
function showSearchModal(){
  isSearchModalVisable.value = true
}
function closeSearchModal(){
  isSearchModalVisable.value = false
}
function updateTable(){
  let storage: string | null
  let storedLocations: string
  storage = localStorage.getItem("SavedForecasts")
  if(typeof storage === 'string'){
    storedLocations = storage
    locations.value = [['','']]
    locations.value = JSON.parse(storedLocations)
    updateCount.value++
  }
}
function autoUpdater(){
  if(autoUpdateCount.value === updateCount.value){
    updateTable()
    autoUpdateCount.value++
  }
  else{
    autoUpdateCount.value = updateCount.value
  }
}
</script>

<template>

  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Hello Bulma!</title>
  </head>
  <body>
  <section class="section">
    <div class="container">
      <div class="is-grouped">
        <button class="button is-primary" @click="showSearchModal">Add Forecast</button>
        <button class="button is-danger" @click="updateTable">Update Table</button>
      </div>
      
      <table class="table ">
        <thead>
            <tr>
                <th>Name</th>
                <th>Country</th>
                <th><abbr title="Temperature">Temp</abbr></th>
                <th><abbr title="Humidty">Hum</abbr></th>
                <th><abbr title="Wind speed">Wind</abbr></th>
                <th><abbr title="Pressure">Press</abbr></th>
                <th><abbr title="Sunrise">Rises</abbr></th>
                <th><abbr title="Sunset">Sets</abbr></th>
                <th>Conditions</th>
                <th>Control</th>
            </tr>
        </thead>
        <tbody>
          <WeatherTableRow  v-for="(location, index) in locations"
          :location="location"
          :updateCount="updateCount"
          :index="index"
          @updated="updateTable">
          </WeatherTableRow>
        </tbody>
      </table>
    </div>
  </section>
  </body>
  <SearchModal
      v-show="isSearchModalVisable"
      @close="closeSearchModal"
      @updated="updateTable"
    />

</template>
