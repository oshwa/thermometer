<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue'
import ThermoComponent from '../assets/thermometer.svg?component'

const props = defineProps({
  title: String,
  unit: String,
  min: Number,
  max: Number,
  keyIncr: String,
  keyDecr: String
})

const count = ref(0)

const thermoContainerRef = ref(null)

// Computed property for scaled value between min and max
const scaledValue = computed(() => {
  const min = props.min || 0
  const max = props.max || 100
  const range = max - min
  return min + (count.value / 100) * range
})

// Computed property to generate dynamic path based on percentage
const currentThermoPath = computed(() => {
  const percentage = Math.max(0, Math.min(100, count.value))

  // Calculate dynamic values based on percentage
  const maxHeight = 438.2527067
  const maxRadius = 28.2684533
  const maxCornerRadius = 15.6601963

  // Calculate minimum values for very low percentages
  const minHeight = 24.52061
  const minRadius = 1.58164
  const minCornerRadius = 0.8762

  // Linear interpolation for height and radius based on percentage
  const height = (maxHeight - minHeight) * percentage / 100 + minHeight
  const radius = (maxRadius - minRadius) * percentage / 100 + minRadius
  const cornerRadius = (maxCornerRadius - minCornerRadius) * percentage / 100 + minCornerRadius

  return `
m 9.3205229,438.40355 3e-6,-${height}
c 0,-${cornerRadius} -11.14611,-${radius} -24.990234,-${radius}
h -18.712891
v 0
c -13.844124,0 -24.990234,${radius - cornerRadius} -24.990234,${radius}
V 438.40355
`
})

// Computed property for sloth position based on percentage
const currentSlothTransform = computed(() => {
  const percentage = Math.max(0, Math.min(100, count.value))

  // Move sloth up the thermometer based on percentage
  // Base position is at the bottom, move up as percentage increases
  const maxMoveDistance = 270 // Maximum pixels to move up
  const moveY = -(maxMoveDistance * percentage) / 100

  return `translate(0, ${moveY})`
})

// Function to update the SVG path and sloth position
const updateThermoPath = () => {
  if (thermoContainerRef.value) {
    const thermoElement = thermoContainerRef.value.querySelector('#thermo')
    if (thermoElement) {
      thermoElement.setAttribute('d', currentThermoPath.value)
    }

    const slothElement = thermoContainerRef.value.querySelector('#sloth')
    if (slothElement) {
      // Get the original transform and replace only the translate part
      const originalTransform = slothElement.getAttribute('transform') || ''
      const baseTransform = originalTransform.replace(/translate\([^)]*\)/g, '').trim()
      const newTransform = baseTransform ? `${baseTransform} ${currentSlothTransform.value}` : currentSlothTransform.value
      slothElement.setAttribute('transform', newTransform)
    }
  }
}

// Functions to update count
const incrementCount = () => {
  count.value = Math.min(100, count.value + 2)
}

const decrementCount = () => {
  count.value = Math.max(0, count.value - 2)
}

// Keyboard event handler
const handleKeyPress = (event) => {
  // Only respond to keyboard events when this component area is focused
  if (event.key === props.keyIncr) {
    event.preventDefault()
    incrementCount()
  } else if (event.key === props.keyDecr) {
    event.preventDefault()
    decrementCount()
  }
}

// Watch for changes in count and update the path
watch(count, () => {
  updateThermoPath()
})

// Update path on component mount and add keyboard listener
onMounted(() => {
  updateThermoPath()
  document.addEventListener('keydown', handleKeyPress)
})

// Cleanup keyboard listener
onUnmounted(() => {
  document.removeEventListener('keydown', handleKeyPress)
})
</script>

<template>
  <div class="card" tabindex="0" @keydown="handleKeyPress">
    <h1>{{ title }}</h1>
    <h2>{{ Math.round(scaledValue * 10) / 10 }}{{ unit }}</h2>
    <div id="thermo-container" ref="thermoContainerRef">
      <ThermoComponent />
    </div>
    <button type="button" @click="incrementCount">+</button>
    <button type="button" @click="decrementCount">-</button>
  </div>

</template>

<style scoped>
.read-the-docs {
  color: #888;
}
#thermo-container {
  display: block;
}

.card {
  outline: none;
}

.card:focus {
  box-shadow: 0 0 0 3px rgba(82, 179, 229, 0.5);
  border-radius: 8px;
}

/* Add smooth transitions for the thermometer animation */
:deep(#thermo) {
  transition: d 0.5s ease-in-out;
}

/* Add smooth transitions for the sloth movement */
:deep(#sloth) {
  transition: transform 0.5s ease-in-out;
}
</style>
