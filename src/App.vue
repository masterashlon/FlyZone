<script setup>
import MainOne from './components/MainOne.vue'
import HeaderOne from './components/HeaderOne.vue';
import FooterOne from './components/FooterOne.vue';
import CookiesModal from './components/CookiesModal.vue';
</script>

<template>
  <div class="container" v-cloak>
    <CookiesModal></CookiesModal>
    <HeaderOne></HeaderOne>
    <MainOne></MainOne>
    <FooterOne></FooterOne>
  </div>
</template>

<style scoped>

[v-cloak] {
  opacity: 0;
}
  .container {
    opacity: 1;
    display: flex;
    flex-direction: column;
    transition: opacity 1s;
  }
</style>
