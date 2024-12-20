<script setup lang="ts">
import axios from 'axios';
import { computed, ref } from 'vue';

const props = defineProps(['location', 'updateCount'])
var updateCount = ref(0)
var result = ref({  name: '',
                    sys:{country:'', 'sunrise':'', 'sunset':''},
                    main:{temp:'', humidity:'', pressure:''},
                    wind:{speed:''},
                    weather:[{icon:''}]
                })
var update = computed(()=>{
    if(props.updateCount > updateCount.value){
        updateData(props.location)
        updateCount.value = props.updateCount
    }
    return 'result'
})

function updateData(location: string[]){
    axios({
  method: 'get',
  url: `https://api.openweathermap.org/data/2.5/weather?lat=${location[0]}&lon=${location[1]}&appid=${import.meta.env.VITE_API_KEY}&units=metric`,
  responseType: 'json'
})
  .then(function (response) {
    result.value = response.data
  });
}
</script>
<template>
<tr class="control" v-if="result.name">
    <td>{{ result.name }}</td>
    <td>{{ result.sys.country }}</td>
    <td>{{ result.main.temp }}</td>    
    <td>{{ result.main.humidity }}</td>
    <td>{{ result.wind.speed }}</td>
    <td>{{ result.main.pressure }}</td>
    <td>{{ result.sys.sunrise }}</td>    
    <td>{{ result.sys.sunset }}</td>
    <td>{{ result.weather[0].icon }}</td>
    <!-- <td><button class="button is-success" @click.prevent="addToStorage()">Add location</button></td> -->
</tr>
<!-- Should never be true, needed to activate the computed function -->
<div v-if="update!=='result'"></div>
</template>