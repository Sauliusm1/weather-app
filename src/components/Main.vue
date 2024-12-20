<script setup lang="ts">
import axios from 'axios'
import {ref, onMounted } from 'vue';
import SearchModal from './SearchModal.vue';
const cityName = 'kaunas'
var isSearchModalVisable = ref(false)
function showSearchModal(){
  isSearchModalVisable.value = true
}
function closeSearchModal(){
  isSearchModalVisable.value = false
}
function updateData(){
axios({
  method: 'get',
  url: `https://api.openweathermap.org/geo/1.0/direct?q=${cityName}&limit=1&appid=${import.meta.env.VITE_API_KEY}`,
  responseType: 'json'
})
  .then(function (response) {
    console.log(response.data)
  });
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
      <div><button class="button is-primary" @click="showSearchModal">Add Forecast</button></div>
      <h1 class="title">
        Hello World
      </h1>
      <p class="subtitle">
        My first website with <strong>Bulma</strong>!
      </p>
    </div>
  </section>
  </body>
  <SearchModal
      v-show="isSearchModalVisable"
      @close="closeSearchModal"
    />
</template>
