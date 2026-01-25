<template>
  <div :class="['doodle-portfolio', { 'dark-mode': isDarkMode }]">
    <!-- Doodle Background Elements -->
    <div class="doodle-bg" ref="doodleBg"></div>

    <div class="container">
      <!-- Header -->
      <header>
        <div class="header-content">
          <div class="logo">
            CaptainFulminyx
          </div>
          <button class="theme-toggle" @click="toggleTheme">
            <i :class="[themeIcon]"></i>
            <span>{{ themeText }}</span>
          </button>
        </div>
      </header>

      <!-- Hero Section -->
      <section class="hero">
        <div class="hero-content">
          <h1><span>{{ name }}</span></h1>
          <div class="hero-image">
            <div class="doodle-avatar floating">
              <img src="../assets/img.jpg" />
          </div>
        </div>
      </div>
      <div class="hero-content" id="social">
        <p>
          {{ bio }}
        </p>
        <div class="social-links">
          <a
            v-for="link in socialLinks"
            :key="link.id"
            :href="link.url"
            class="social-link"
            target="_blank"
            rel="noopener noreferrer"
            >
            <i :class="link.icon"></i>
          </a>

        </div>
      </div>
    </section>


    <!-- Portfolio Section -->
    <section class="portfolio">
      <h2 class="section-title">My Projects</h2>
      <div class="portfolio-grid">
        <div
          v-for="project in projects"
          :key="project.id"
          class="portfolio-card"
          @mouseenter="hoverCard(project.id)"
          @mouseleave="unhoverCard(project.id)"
          :style=" { transform: hoveredCard === project.id ? 'translateY(-10px)': '' }"
          >
          <div class="portfolio-icon">
            <i :class="project.icon"></i>
          </div>
          <h3>{{ project.title }}</h3>
          <p>
            {{ project.description }}
          </p>
        </div>
      </div>
    </section>

    <!-- Skills Section -->
    <section class="skills">
      <h2 class="section-title">
        Most Used Skills & Techs</h2>
      <div class="skills-container">
        <div
          v-for="(skill, index) in skills"
          :key="index"
          class="skill-item"
          @click="rotateSkill(index)"
          >
          {{ skill }}
        </div>
      </div>
    </section>

    <!-- Contact Section -->
    <section class="contact-section" id="contact">
      <h2 class="section-title">Get In Touch</h2>
      <p>
        {{ contactMessage }}
      </p>
      <a href="mailto: captainfulminyx@gmail.com" class="cta-button">Let's Create Together</a>

    </section>

    <!-- Footer -->
    <footer>
      <p>
        © {{ currentYear }} DoodleFolio. All rights reserved. Handcrafted with creativity.
      </p>
    </footer>
  </div>
</div>
</template>

<script setup>
import {
ref,
computed,
onMounted,
onUnmounted
} from 'vue'

// Theme state
const isDarkMode = ref(false)
const themeIcon = computed(() => isDarkMode.value ? 'fas fa-sun': 'fas fa-moon')
const themeText = computed(() => isDarkMode.value ? 'Light Mode': 'Dark Mode')

// Data
const name = ref('Fulminyx')
const bio = ref('A passionate programmer driven by curiosity and problem-solving, combining analytical thinking with a love for thoughtful design. I enjoy building meaningful digital experiences shaped by patterns, people, and the stories hidden in data.')
const contactMessage = ref("I'm looking for opportunities where I can contribute my technical expertise, grow alongside experienced developers, and help create products that make a real impact.")

const projects = ref([{
id: 1,
title: 'Design System',
description: 'A comprehensive design system with doodle aesthetics for web applications. Includes components, patterns, and guidelines.',
icon: 'fas fa-paint-brush'
},
{
id: 2,
title: 'Interactive Website',
description: 'An engaging website with hand-drawn animations and playful interactions that enhance user experience.',
icon: 'fas fa-code'
},
{
id: 3,
title: 'Mobile App UI',
description: 'A sketch-style mobile application interface with intuitive navigation and delightful micro-interactions.',
icon: 'fas fa-mobile-alt'
}])

const skills = ref([
'UI/UX Design',
'Frontend Development',
'Illustration',
'Animation',
'Responsive Design',
'JavaScript',
'Vue.js',
'React',
'Figma'
])

const socialLinks = ref([{
id: 1, name: 'GitHub', icon: 'fab fa-github', url: 'https://github.com'
},
{
id: 2, name: 'LinkedIn', icon: 'fab fa-linkedin-in', url: 'https://linkedin.com'
},
{
id: 3, name: 'Dribbble', icon: 'fab fa-dribbble', url: 'https://dribbble.com'
},
{
id: 4, name: 'Behance', icon: 'fab fa-behance', url: 'https://behance.net'
},
{
id: 5, name: 'Email', icon: 'fas fa-envelope', url: 'mailto:hello@example.com'
}])

// Interactive states
const hoveredCard = ref(null)
const rotatedSkills = ref( {})

// Computed properties
const currentYear = computed(() => new Date().getFullYear())

// Methods
const toggleTheme = () => {
isDarkMode.value = !isDarkMode.value
localStorage.setItem('portfolio-theme', isDarkMode.value ? 'dark': 'light')
}

const hoverCard = (id) => {
hoveredCard.value = id
}

const unhoverCard = () => {
hoveredCard.value = null
}

const rotateSkill = (index) => {
const rotations = [-5,
5,
-10,
10,
0]
const randomRotation = rotations[Math.floor(Math.random() * rotations.length)]

// Create or update the rotation for this skill
rotatedSkills.value = {
...rotatedSkills.value,
[index]: randomRotation
}
}

const createDoodleBackground = () => {
const doodleBg = document.querySelector('.doodle-bg')
if (!doodleBg) return

// Clear existing doodles
doodleBg.innerHTML = ''

// Colors based on current theme
const colors = isDarkMode.value
? ['#8a84ff',
'#ff7b9c',
'#5ce1e6',
'#f0f0f0']: ['#6c63ff',
'#ff6584',
'#36d1dc',
'#333333']

// Create lines
const lineCount = 30
for (let i = 0; i < lineCount; i++) {
const line = document.createElement('div')
line.classList.add('doodle-line')

// Random properties
const width = Math.random() * 100 + 50
const height = Math.random() * 4 + 1
const top = Math.random() * 100
const left = Math.random() * 100
const rotation = Math.random() * 360
const color = colors[Math.floor(Math.random() * colors.length)]
const opacity = Math.random() * 0.3 + 0.1

// Apply properties
line.style.width = `${width}px`
line.style.height = `${height}px`
line.style.top = `${top}%`
line.style.left = `${left}%`
line.style.transform = `rotate(${rotation}deg)`
line.style.backgroundColor = color
line.style.opacity = opacity

// Add animation
line.style.animation = `float ${Math.random() * 10 + 10}s ease-in-out infinite`
line.style.animationDelay = `${Math.random() * 5}s`

doodleBg.appendChild(line)
}

// Add circles
const circleCount = 15
for (let i = 0; i < circleCount; i++) {
const circle = document.createElement('div')
circle.classList.add('doodle-line')

// Random properties
const size = Math.random() * 20 + 5
const top = Math.random() * 100
const left = Math.random() * 100
const color = colors[Math.floor(Math.random() * colors.length)]
const opacity = Math.random() * 0.2 + 0.05

// Apply properties
circle.style.width = `${size}px`
circle.style.height = `${size}px`
circle.style.top = `${top}%`
circle.style.left = `${left}%`
circle.style.borderRadius = '50%'
circle.style.backgroundColor = color
circle.style.opacity = opacity

// Add animation
circle.style.animation = `float ${Math.random() * 8 + 8}s ease-in-out infinite`
circle.style.animationDelay = `${Math.random() * 3}s`

doodleBg.appendChild(circle)
}
}

// Lifecycle hooks
onMounted(() => {
// Check saved theme preference
const savedTheme = localStorage.getItem('portfolio-theme')
const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches

isDarkMode.value = savedTheme === 'dark' || (!savedTheme && prefersDark)

// Create doodle background
createDoodleBackground()

// Update on resize
window.addEventListener('resize', createDoodleBackground)
})

onUnmounted(() => {
window.removeEventListener('resize', createDoodleBackground)
})

// Watch for theme changes to update doodle background
import {
watch
} from 'vue'
watch(isDarkMode, () => {
setTimeout(createDoodleBackground, 100)
})
</script>