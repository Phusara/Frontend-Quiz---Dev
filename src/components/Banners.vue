<!-- BannerSlider.vue -->
<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'

const slider = ref(null)
const isHovered = ref(false)

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
  if (slider.value) {
    slider.value.scrollBy({ left: -10, behavior: 'smooth' })
  }
}

const scrollRight = () => {
  if (slider.value) {
    slider.value.scrollBy({ left: 10, behavior: 'smooth' })
  }
}

const startScrollLeft = () => {
  if (scrollInterval.value) return
  scrollInterval.value = setInterval(scrollLeft, 50) 
}

const startScrollRight = () => {
  if (scrollInterval.value) return
  scrollInterval.value = setInterval(scrollRight, 50) 
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
    if (!slider.value || isHovered.value) return
    
    const maxScrollLeft = slider.value.scrollWidth - slider.value.clientWidth
    
    // Check if banner at the end (with a small tolerance)
    if (slider.value.scrollLeft >= maxScrollLeft - 5) {
      // Reset to beginning
      slider.value.scrollTo({ left: 0, behavior: 'auto' })
    } else {
      
      slider.value.scrollBy({ left: 1, behavior: 'auto' })
    }
  }, 50) 
}

const stopAutoScroll = () => {
  if (autoScrollInterval.value) {
    clearInterval(autoScrollInterval.value)
    autoScrollInterval.value = null
  }
}

const handleMouseEnter = () => {
  isHovered.value = true
}

const handleMouseLeave = () => {
  isHovered.value = false
}

onMounted(() => {
  // Add a small delay to ensure DOM ready
  setTimeout(() => {
    startAutoScroll()
  }, 100)
})

onBeforeUnmount(() => {
  stopAutoScroll()
  stopScroll()
})
</script>

<template>
  <div 
    class="relative"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
  >
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
      class="absolute left-2 top-1/2 -translate-y-1/2 p-2 rounded-full text-[38px] z-10"
    >
            <i class="fa fa-arrow-left"></i>

    </button>
    <button
      @mousedown="startScrollRight"
      @mouseup="stopScroll"
      @mouseleave="stopScroll"
      @touchstart="startScrollRight"
      @touchend="stopScroll"
      @click="scrollRight"
      class="absolute right-2 top-1/2 -translate-y-1/2  p-2 rounded-full text-[38px] z-10"
    >
      <i class="fa fa-arrow-right"></i>
    </button>
  </div>
</template>
<style scoped>
.flex::-webkit-scrollbar {
  display: none;
}

.flex {
  -ms-overflow-style: none; 
  scrollbar-width: none; 
}
</style>
