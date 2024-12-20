<script setup lang="ts">
const props = defineProps(['result'])
function addToStorage(){
    let forecasts: string
    let storage : string | null
    let locations :[string[]]
    let location: string[] = ['','']
    location[0] = props.result.lat
    location[1] = props.result.lon
    storage =  localStorage.getItem("SavedForecasts")
    if(typeof storage === 'string'){
        forecasts = storage
        locations = JSON.parse(forecasts)
        locations[locations.length+0] = location
        localStorage.setItem("SavedForecasts",JSON.stringify(locations))
    }
    else{
        localStorage.setItem("SavedForecasts",JSON.stringify([location]))
    }

}
</script>
<template>
<tr class="control" v-if="result.name">
    <td>{{ result.name }}</td>
    <td>{{ result.country }}</td>
    <td>{{ result.lat }}</td>    
    <td>{{ result.lon }}</td>
    <td><button class="button is-success" @click.prevent="addToStorage()">Add location</button></td>
</tr>
</template>