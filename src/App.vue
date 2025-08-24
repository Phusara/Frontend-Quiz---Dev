<script setup>
import Banners from './components/Banners.vue';
import bookList from './components/bookList.vue';
</script>

<template>
  <div class="w-full h-full">
  <Banners></Banners>
  <div class="mt-10 ml-6">
  <h1 class="noto-sans-thai text-3xl font-bold">รายการที่คั่นไว้</h1>
</div>
 <div class="h-5 border-b-2 border-solid border-gray-300"></div>
  <bookList></bookList>
  </div>
  
</template>
s
<style scoped>
.noto-sans-thai {
  font-family: 'Noto Sans Thai', sans-serif;
}

</style>
