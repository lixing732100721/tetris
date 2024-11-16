<script setup>
import { ref } from 'vue'

const randomNumber = ref(Math.floor(Math.random() * 100) + 1)
const userGuess = ref('')
const message = ref('')
const attempts = ref(0)

const checkGuess = () => {
  const guess = parseInt(userGuess.value)
  attempts.value++
  
  if (isNaN(guess)) {
    message.value = '请输入有效的数字！'
  } else if (guess === randomNumber.value) {
    message.value = `恭喜你！猜对了！用了 ${attempts.value} 次。`
  } else if (guess < randomNumber.value) {
    message.value = '太小了，再试试！'
  } else {
    message.value = '太大了，再试试！'
  }
  userGuess.value = ''
}

const resetGame = () => {
  randomNumber.value = Math.floor(Math.random() * 100) + 1
  userGuess.value = ''
  message.value = ''
  attempts.value = 0
}
</script>

<template>
  <div class="guess-game">
    <h2>猜数字游戏</h2>
    <p>猜一个 1-100 之间的数字</p>
    
    <div class="game-container">
      <input 
        type="number" 
        v-model="userGuess"
        @keyup.enter="checkGuess"
        placeholder="输入你的猜测"
      >
      <button @click="checkGuess">猜！</button>
      <button @click="resetGame">重新开始</button>
    </div>
    
    <p class="message">{{ message }}</p>
    <p>已猜次数: {{ attempts }}</p>
  </div>
</template>

<style scoped>
.guess-game {
  max-width: 400px;
  margin: 0 auto;
  text-align: center;
}

.game-container {
  margin: 20px 0;
}

input {
  padding: 5px;
  margin-right: 10px;
}

button {
  margin: 0 5px;
  padding: 5px 15px;
  background-color: #42b883;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #3aa876;
}

.message {
  margin: 20px 0;
  font-weight: bold;
}
</style> 