<script setup>
import herobg from "@/assets/img/herobg-1.webp";
import { defineProps } from "vue";

defineProps({
  title: {
    type: String,
    default: '"Shape Your Future, Craft Your Career"',
  },
});
</script>

<template>
  <section
    class="bg-blue-900 py-20 mb-4 relative"
    style="
      background-image: url('/src/assets/img/herobg-1.webp');
      background-size: cover;
      background-position: center;
    "
  >
    <!-- Background Overlay -->
    <div class="absolute inset-8 bg-black opacity-70"></div>

    <div
      class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 flex flex-col items-center relative z-10"
    >
      <div class="text-center">
        <h1 class="text-3xl text-white sm:text-4xl md:text-5xl">
          {{ title }}
        </h1>
        <p class="my-4 text-1xl text-white justify-center">
          Find the jobs that fit your skills and needs
        </p>
      </div>
    </div>
  </section>
</template>

<style scoped>
.hero-section {
  background-image: url("/src/assets/img/herobg-1.webp");
  background-size: cover;
  background-position: center;
  position: relative;
}

.hero-section::before {
  content: "";
  position: absolute;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(5px);
  z-index: 1;
}

.hero-content {
  position: relative;
  z-index: 1;
}
</style>
