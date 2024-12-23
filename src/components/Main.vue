<script setup lang="ts">
import {ref, onMounted } from 'vue';
import SearchModal from './SearchModal.vue';
import WeatherTableRow from './Rows/WeatherTableRow.vue';
var locations = ref([['','']])
var paginatedLocations = ref([['','']])
var currentFilter = ref('')
var updateCount = ref(0)
var autoUpdateCount = ref(0)
var isSearchModalVisable = ref(false)
var isNotificationVisable = ref(false)
var notificationMessage = ref('')
var notificationTimeoutId = ref(0)
//Pagination variables
var currentPage = ref(0)
var totalPages = ref (-1)
var pageSize = ref(10)
//Called once the site is opened
onMounted(() => {
    if(updateCount.value===0){
      updateTable()
      setInterval(autoUpdater, 3000000);
    }
})
function showSearchModal(){
  isSearchModalVisable.value = true
}
function closeSearchModal(){
  isSearchModalVisable.value = false
}
function showNotification(message : string){
  closeNotification()
  notificationMessage.value = message
  isNotificationVisable.value = true
  notificationTimeoutId.value = setTimeout(closeNotification, 60000)
}
function closeNotification(){
  clearTimeout(notificationTimeoutId.value)
  isNotificationVisable.value = false
}

function switchPage(pageNum : number){
    currentPage.value = pageNum
}
function firstPage(){
  if(currentPage.value !== 1){  
    switchPage(1)
    updateTable()
  }
}
function nextPage(){
    if (currentPage.value < totalPages.value) {
        switchPage(currentPage.value + 1)
        updateTable()
    }
}
function previousPage(){
    if (currentPage.value > 1) {
        switchPage(currentPage.value - 1)
        updateTable()
    }
}
function lastPage(){
  if(currentPage.value !== totalPages.value){  
    switchPage(totalPages.value)
    updateTable()
  }
}
//Updates pagination and changes the shown locations as needed
function updateTable(){
  let storage: string | null
  let storedLocations: string
  storage = localStorage.getItem("SavedForecasts")
  if(typeof storage === 'string'){
    storedLocations = storage
    locations.value = [['','']]
    locations.value = JSON.parse(storedLocations)
    updateCount.value++
    totalPages.value = Math.ceil(locations.value.length/pageSize.value)
    if(currentPage.value > totalPages.value){
      currentPage.value--
    }
    if(currentPage.value < 1 && locations.value.length > 0){
      currentPage.value = 1
    }
    if(currentPage.value === totalPages.value){
      paginatedLocations.value = locations.value.slice((currentPage.value - 1) * pageSize.value)
    }
    else{
      paginatedLocations.value = locations.value.slice((currentPage.value - 1) * pageSize.value,currentPage.value * pageSize.value)
    }
    showNotification("Table updated")
  }
}
//Calls updateTable if there has not been a recent manually caused update
//If it has, skips the current automatic update
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
<body>
  <section class="section">
    <div class="container">
      <div class="columns">
        <button class="button is-primary column is-fullwitdh" @click="showSearchModal">Add Forecast</button>
      </div>

      <nav class="pagination is-centered" role="navigation" aria-label="pagination" v-show="currentPage>0">
        <button class="pagination-previous" :class="{'is-disabled':currentPage === 1}" @click.prevent="previousPage">Previous page</button>
        <button class="pagination-next" :class="{'is-disabled':currentPage === totalPages}" @click.prevent="nextPage">Next page</button>
        <ul class="pagination-list">
          <li>
            <button class="pagination-link" aria-label="Goto page 1" :class="{'is-disabled':currentPage === 1}" @click.prevent="firstPage">1</button>
          </li>
          <li>
            <span class="pagination-ellipsis">&hellip;</span>
          </li>
          <li v-show="currentPage - 1 > 0">
            <button class="pagination-link" aria-label="Goto previous page" @click.prevent="previousPage">{{ currentPage - 1 }}</button>
          </li>
          <li>
            <button
              class="pagination-link is-current"
              aria-label="Current page"
              aria-current="page"
              >{{ currentPage }}</button
            >
          </li>
          <li v-show="currentPage < totalPages">
            <button class="pagination-link" aria-label="Goto next page" @click.prevent="nextPage">{{ currentPage + 1 }}</button>
          </li>
          <li>
            <span class="pagination-ellipsis">&hellip;</span>
          </li>
          <li>
            <button class="pagination-link" aria-label="Goto last page" :class="{'is-disabled':currentPage === totalPages}" @click.prevent="lastPage">{{totalPages}}</button>
          </li>
        </ul>
      </nav>

      <div class="control">
        <input class="input" v-model="currentFilter">
      </div>
      <table class="table is-fullwidth">
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
          <WeatherTableRow v-for="(location, index) in paginatedLocations"
          :location="location"
          :updateCount="updateCount"
          :index="index"
          :currentPage="currentPage"
          :pageSize="pageSize"
          :filter="currentFilter"
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

<div v-show="isNotificationVisable" class="notification is-success">
  <button class="delete" @click.prevent="closeNotification"></button>
    {{ notificationMessage }}
</div>

</template>
