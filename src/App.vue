<script setup>
import { RouterView } from 'vue-router'
import Header from './components/Header.vue'
</script>

<template>
  <div class="myApp">
    <Header />
    <RouterView />
  </div>
</template>
