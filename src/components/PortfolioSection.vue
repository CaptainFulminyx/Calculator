<template>
  <section class="portfolio">
    <h2 class="section-title">My Projects</h2>
    <div class="portfolio-grid">
      <div
        v-for="project in projects"
        :key="project.id"
        class="portfolio-card"
        @mouseenter="hovered = project.id"
        @mouseleave="hovered = null"
        :style=" { transform: hovered === project.id ? 'translateY(-10px)': '' }">
        <div class="image-wrapper">
          <img :src="project.img" />

        <a
          v-if="project.link"
          :href="project.link"
          class="image-link">
          <Icon icon="majesticons:open" width="30" height="30" />
        </a>
      </div>

      <h3>{{ project.title }}</h3>
      <p>
        {{ project.description }}
      </p>
    </div>
  </div>
</section>
</template>

<script setup>
import {
ref
} from 'vue'
import {
Icon
} from '@iconify/vue'
defineProps( {
projects: Array
})
const hovered = ref(null)
</script>