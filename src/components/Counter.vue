<script setup>
import { ref } from 'vue'

const count = ref(0)

const increment = () => {
  count.value++
}

const decrement = () => {
  count.value--
}
</script>

<template>
  <div class="counter">
    <h2>简单计数器</h2>
    <div class="counter-display">
      当前计数: {{ count }}
    </div>
    <div class="counter-buttons">
      <button @click="decrement">-</button>
      <button @click="increment">+</button>
    </div>
  </div>
</template>

<style scoped>
.counter {
  max-width: 400px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
  background: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.counter-display {
  font-size: 2rem;
  margin: 1rem 0;
}

.counter-buttons {
  display: flex;
  gap: 1rem;
  justify-content: center;
}

button {
  font-size: 1.2rem;
  padding: 0.5rem 2rem;
  border: none;
  background: #42b883;
  color: white;
  border-radius: 4px;
  cursor: pointer;
  transition: background 0.3s;
}

button:hover {
  background: #3aa876;
}
</style> 