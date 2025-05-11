<script setup>
import { ref, onUnmounted, computed, watch } from 'vue'

// reactive values
const userInput = ref()
const duration = ref(0)
const elapsed = ref(0)

let lastTime
let handle

// update progress bar
const update = () => {
  elapsed.value = performance.now() - lastTime
  if (elapsed.value >= duration.value) {
    cancelAnimationFrame(handle)
  } else {
    handle = requestAnimationFrame(update)
  }
}

// reset and start timer
const reset = () => {
  elapsed.value = 0
  lastTime = performance.now()
  update()
}

// automatically restart timer when userInput changes
watch(userInput, (val) => {
  duration.value = val * 1000
  reset()
})

// computed progress
const progressRate = computed(() =>
  Math.min(elapsed.value / duration.value, 1)
)

// cleanup
onUnmounted(() => {
  cancelAnimationFrame(handle)
})
</script>

<template>
  <h1>Timer</h1>

  <div class="class">
    <h2>Enter Duration Time:</h2>
    <input 
      type="number"
      v-model="userInput"
      placeholder="In seconds"
    />
  </div>

  <div class="elapsed-container">
    <label>
      Elapsed Time: <progress :value="progressRate" max="1"></progress>
    </label>
    <div>{{ (elapsed / 1000).toFixed(1) }}s</div>
  </div>

  <div>
    Duration: 
    <input type="range" v-model="duration" min="1" :max="userInput * 1000" />
    {{ (duration / 1000).toFixed(1) }}s
  </div>

  <button @click="reset">Reset</button>
</template>

<style>
:root {
  font-family: 'Times New Roman', Times, serif;
  line-height: 1.5;
  font-weight: 400;
  color-scheme: light dark;
  color: rgba(255, 255, 255, 0.87);
  background-color: #242424;
  font-synthesis: none;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
body {
  margin: 0;
  display: flex;
  place-items: center;
  min-width: 320px;
  min-height: 100vh;
}
h1 {
  font-size: 3.2em;
  line-height: 1.1;
}
button {
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.6em 1.2em;
  font-size: 1em;
  font-weight: 500;
  font-family: inherit;
  background-color: #1a1a1a;
  cursor: pointer;
  transition: border-color 0.25s;
}
button:hover {
  border-color: #646cff;
}
button:focus,
button:focus-visible {
  outline: 4px auto -webkit-focus-ring-color;
}
#app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
}
.class {
  display: flex;
  align-items: center;
}
input[type='number'] {
  width: 80px;
  height: 30px;
  margin-left: 10px;
  margin-top: 15px;
}
.elapsed-container {
  width: 300px;
  display: flex;
  align-items: center;
  margin-top: 20px;
}
.elapsed-bar {
  background-color: red;
  height: 10px;
}
@media (prefers-color-scheme: light) {
  :root {
    color: #213547;
    background-color: #ffffff;
  }
  button {
    background-color: #f9f9f9;
  }
}
</style>
