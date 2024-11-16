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

<style>
/* 添加全局样式 */
body {
  margin: 0;
  padding: 0;
  overflow: hidden;
  position: fixed;
  width: 100%;
  height: 100%;
}

/* 响应式样式 */
@media (max-width: 768px) {
  header {
    padding: 10px;
  }

  .logo {
    width: 80px;
    height: 80px;
    margin: 0 auto 1rem;
  }

  h1 {
    font-size: 1.5rem;
    margin-bottom: 1rem;
  }

  main {
    padding: 0;
  }
}
</style>
