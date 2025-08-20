<!-- BannerSlider.vue -->
<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'

const slider = ref(null)

const banners = [
  { title: 'Banner 1', image: '../src/assets/image.png' },
  { title: 'Banner 2', image: '../src/assets/image.png' },
  { title: 'Banner 3', image: '../src/assets/image.png' },
  { title: 'Banner 4', image: '../src/assets/image.png' },
  { title: 'Banner 5', image: '../src/assets/image.png' },
  { title: 'Banner 6', image: '../src/assets/image.png' },
  { title: 'Banner 7', image: '../src/assets/image.png' },
  { title: 'Banner 8', image: '../src/assets/image.png' },
  // Add more banners as needed
]

const scrollInterval = ref(null)
const autoScrollInterval = ref(null)

const scrollLeft = () => {
  slider.value?.scrollBy({ left: -10, behavior: 'smooth' })
}

const scrollRight = () => {
  slider.value?.scrollBy({ left: 10, behavior: 'smooth' })
}

const startScrollLeft = () => {
  if (scrollInterval.value) return
  scrollInterval.value = setInterval(scrollLeft, 16)
}

const startScrollRight = () => {
  if (scrollInterval.value) return
  scrollInterval.value = setInterval(scrollRight, 16)
}

const stopScroll = () => {
  if (scrollInterval.value) {
    clearInterval(scrollInterval.value)
    scrollInterval.value = null
  }
}

const startAutoScroll = () => {
  if (autoScrollInterval.value) return
  autoScrollInterval.value = setInterval(() => {
    if (!slider.value) return
    // If at the end, reset scrollLeft to 0
    const maxScrollLeft = slider.value.scrollWidth - slider.value.clientWidth
    if (slider.value.scrollLeft >= maxScrollLeft - 2) {
      slider.value.scrollTo({ left: 0, behavior: 'auto' })
    } else {
      slider.value.scrollBy({ left: 2, behavior: 'smooth' })
    }
  }, 16)
}

const stopAutoScroll = () => {
  if (autoScrollInterval.value) {
    clearInterval(autoScrollInterval.value)
    autoScrollInterval.value = null
  }
}

onMounted(() => {
  startAutoScroll()
})

onBeforeUnmount(() => {
  stopAutoScroll()
})
</script>

<template>
  <div class="relative">
    <!-- Scrollable container -->
    <div
      ref="slider"
      class="flex overflow-x-auto scroll-smooth snap-mandatory gap-4 p-4"
    >
      <div
        v-for="(banner, index) in banners"
        :key="index"
        class="shrink-0 w-[300px] sm:w-[400px] md:w-[500px] snap-start bg-white rounded-lg shadow"
      >
        <img :src="banner.image" :alt="banner.title" class="w-full h-auto rounded-lg" />
      </div>
    </div>

    <!-- Navigation buttons -->
    <button
      @mousedown="startScrollLeft"
      @mouseup="stopScroll"
      @mouseleave="stopScroll"
      @touchstart="startScrollLeft"
      @touchend="stopScroll"
      @click="scrollLeft"
      class="absolute left-2 top-1/2 -translate-y-1/2 bg-gray-200 hover:bg-gray-300 p-2 rounded-full z-10"
    >
      ←
    </button>
    <button
      @mousedown="startScrollRight"
      @mouseup="stopScroll"
      @mouseleave="stopScroll"
      @touchstart="startScrollRight"
      @touchend="stopScroll"
      @click="scrollRight"
      class="absolute right-2 top-1/2 -translate-y-1/2 bg-gray-200 hover:bg-gray-300 p-2 rounded-full z-10"
    >
      →
    </button>
  </div>
</template>
<style scoped>
/* Hide scrollbar for Chrome, Safari and Opera */
.flex::-webkit-scrollbar {
  display: none;
}
/* Hide scrollbar for IE, Edge and Firefox */
.flex {
  -ms-overflow-style: none; /* IE and Edge */
  scrollbar-width: none; /* Firefox */
}
</style>
