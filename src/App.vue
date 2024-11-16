<script setup>
import { ref } from 'vue'
import Tetris from './components/Tetris.vue'

// 添加热重载标记
if (import.meta.hot) {
  import.meta.hot.accept()
}

const title = ref('俄罗斯方块')
</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />
    <h1>{{ title }}</h1>
  </header>

  <main>
    <Tetris />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
  text-align: center;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

h1 {
  color: #42b883;
  margin-bottom: 2rem;
}
</style>
