<script setup lang="ts">
defineProps(['result'])
</script>
<template>
<tr class="control">
    <td>{{ result.name }}</td>
    <td>{{ result.country }}</td>
    <td>{{ result.lat }}</td>    
    <td>{{ result.lon }}</td>
    <td><button class="button is-success">Add location</button></td>
</tr>
</template>