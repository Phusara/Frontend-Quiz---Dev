<script setup>
import { onMounted, ref } from 'vue';
import { getItems } from '../../utils/fetchUtils.js'; 

const books = ref([]);
const itemUrl = `${import.meta.env.VITE_APP_URL}/books`
onMounted(async () => {
  try {
    books.value = await getItems(itemUrl);
    console.log(books.value);
  } catch (error) {
    console.error('Failed to fetch books:', error);
    // Optionally show a user-friendly message in the UI
  }
});


</script>

<template>
<div class="h-full w-full 2 p-2 border rounded shadow bg-white">
    <div class="flex flex-wrap justify-around ">
  <div v-for="(book, index) in books" :key="index" class="m-2 p-2 border rounded shadow bg-white w-full sm:w-[48%] lg:w-[30%]">
    <div class="flex relative">
    <img src="/public/bookcover.jpg" class="w-[35%]" alt="Book Cover" />
    <div class="pl-6">
    <p>{{ book.name }}</p>
    <p class="pt-4">Author: {{ book.author }}</p>
    <p class=" absolute bottom-0">Relese {{ book.date }}</p>
</div>
</div>
  </div>
  </div>
  </div>
</template>
<style scoped>

</style>
