<script setup lang="ts">
import axios from 'axios';
import { computed, ref } from 'vue';
const emit = defineEmits(['updated'])
const props = defineProps(['location', 'updateCount','index','filter','pageSize','currentPage'])
var imgLink = ref("https://openweathermap.org/img/wn/${result.weather[0].icon}@2x.png")
var updateCount = ref(0)
var result = ref({  name: '',
                    sys:{country:'', 'sunrise':'', 'sunset':''},
                    main:{temp:'', humidity:'', pressure:''},
                    wind:{speed:''},
                    weather:[{icon:''}]
                })
var filtered = ref(true)
//Gets triggered everytime props change
var update = computed(()=>{
  //Only update the data when the parent requests an update
  if(props.updateCount > updateCount.value){
      updateData(props.location)
      updateCount.value = props.updateCount
  }
  filtered.value = filterSelf(props.filter)
  return 'result'
})
//Calls the API to get refreshed data
function updateData(location: string[]){
  axios({
    method: 'get',
    url: `https://api.openweathermap.org/data/2.5/weather?lat=${location[0]}&lon=${location[1]}&appid=${import.meta.env.VITE_API_KEY}&units=metric`,
    responseType: 'json'
  })
  .then(function (response) {
    result.value = response.data
    imgLink.value =`https://openweathermap.org/img/wn/${result.value.weather[0].icon}@2x.png`
  });
}
//Function that gets called when the remove location button is pressed
function removeFromStorage(index : number){
  let forecasts: string
  let storage: string | null
  let locations: [string[]]
  storage =  localStorage.getItem("SavedForecasts")
  if(typeof storage === 'string'){
      forecasts = storage
      locations = JSON.parse(forecasts)
      locations.splice((props.currentPage - 1) * props.pageSize+index,1)
      localStorage.setItem("SavedForecasts",JSON.stringify(locations))
      emit('updated')
  }
}
//Checks if any of the location results contain the filter 
function filterSelf(filter : string){
  filter = filter.toLowerCase()
  if(filter===''){
    return true
  }
  if(result.value.name.toLowerCase().includes(filter)) {
    return true
  }
  if(result.value.sys.country.toLowerCase().includes(filter)){
    return true
  }
  if(result.value.main.temp.toString().toLowerCase().includes(filter)){
    return true
  }
  if(result.value.main.humidity.toString().toLowerCase().includes(filter)){
    return true
  }
  if(result.value.wind.speed.toString().toLowerCase().includes(filter)){
    return true
  }
  if(result.value.main.pressure.toString().toLowerCase().includes(filter)){
    return true
  }
  if(result.value.sys.sunrise.toString().toLowerCase().includes(filter)){
    return true
  }
  if(result.value.sys.sunset.toString().toLowerCase().includes(filter)){
    return true
  }
  return false
}
</script>
<template>
<tr class="control has-text-centered"  v-if="result.name" v-show="filtered">
    <td class="has-text-centered">{{ result.name }}</td>
    <td>{{ result.sys.country }}</td>
    <td>{{ result.main.temp }}</td>    
    <td>{{ result.main.humidity }}</td>
    <td>{{ result.wind.speed }}</td>
    <td>{{ result.main.pressure }}</td>
    <td>{{ result.sys.sunrise }}</td>    
    <td>{{ result.sys.sunset }}</td>
    <td><img :src="imgLink" class="image is-64x64"></td>
    <td><button class="button is-danger" @click.prevent="removeFromStorage(props.index)">Remove location</button></td>
</tr>
<!-- Should never be true, needed to activate the computed function -->
<div v-if="update!=='result'"></div>
</template>