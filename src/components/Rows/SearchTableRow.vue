<script setup lang="ts">
const props = defineProps(['result'])
const emit = defineEmits(['updated'])
//Function that gets called when the add location button is pressed
function addToStorage(){
    let forecasts: string
    let storage: string | null
    let locations: [string[]]
    let location: string[] = ['','']
    location[0] = props.result.lat
    location[1] = props.result.lon
    storage =  localStorage.getItem("SavedForecasts")
    //If storage exists add the location to the array
    if(typeof storage === 'string'){
        forecasts = storage
        locations = JSON.parse(forecasts)
        locations[locations.length+0] = location
        localStorage.setItem("SavedForecasts",JSON.stringify(locations))
    }
    else{
        //If storage does not exist - make a new array
        localStorage.setItem("SavedForecasts",JSON.stringify([location]))
    }
    //Send update notification to parent
    emit('updated')

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