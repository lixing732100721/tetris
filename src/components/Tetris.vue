<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const BOARD_WIDTH = 10
const BOARD_HEIGHT = 20
const BLOCK_SIZE = 30

const board = ref(Array(BOARD_HEIGHT).fill().map(() => Array(BOARD_WIDTH).fill(0)))
const currentPiece = ref(null)
const nextPiece = ref(null)
const gameInterval = ref(null)
const score = ref(0)
const level = ref(1)
const gameOver = ref(false)
const isPaused = ref(false)

// 定义方块形状
const SHAPES = [
  [[1, 1, 1, 1]], // I
  [[1, 1], [1, 1]], // O
  [[1, 1, 1], [0, 1, 0]], // T
  [[1, 1, 1], [1, 0, 0]], // L
  [[1, 1, 1], [0, 0, 1]], // J
  [[1, 1, 0], [0, 1, 1]], // S
  [[0, 1, 1], [1, 1, 0]], // Z
]

// 新增：方块颜色
const COLORS = [
  '#FF0D72', // I
  '#0DC2FF', // O
  '#0DFF72', // T
  '#F538FF', // L
  '#FF8E0D', // J
  '#FFE138', // S
  '#3877FF'  // Z
]

// 生成新方块
function generatePiece() {
  const index = Math.floor(Math.random() * SHAPES.length)
  return {
    shape: SHAPES[index],
    color: COLORS[index],
    x: Math.floor((BOARD_WIDTH - SHAPES[index][0].length) / 2),
    y: 0
  }
}

// 检查碰撞
function checkCollision(piece, board) {
  for (let y = 0; y < piece.shape.length; y++) {
    for (let x = 0; x < piece.shape[y].length; x++) {
      if (piece.shape[y][x]) {
        const newX = piece.x + x
        const newY = piece.y + y
        if (
          newX < 0 || 
          newX >= BOARD_WIDTH || 
          newY >= BOARD_HEIGHT ||
          (newY >= 0 && board[newY][newX])
        ) {
          return true
        }
      }
    }
  }
  return false
}

// 合并方块到板上
function mergePiece() {
  for (let y = 0; y < currentPiece.value.shape.length; y++) {
    for (let x = 0; x < currentPiece.value.shape[y].length; x++) {
      if (currentPiece.value.shape[y][x]) {
        const newY = currentPiece.value.y + y
        if (newY >= 0) {
          board.value[newY][currentPiece.value.x + x] = currentPiece.value.color
        }
      }
    }
  }
}

// 清除完整的行
function clearLines() {
  let linesCleared = 0
  for (let y = BOARD_HEIGHT - 1; y >= 0; y--) {
    if (board.value[y].every(cell => cell !== 0)) {
      board.value.splice(y, 1)
      board.value.unshift(Array(BOARD_WIDTH).fill(0))
      linesCleared++
      y++
    }
  }
  if (linesCleared > 0) {
    score.value += linesCleared * 100 * level.value
    level.value = Math.floor(score.value / 1000) + 1
    if (gameInterval.value) {
      clearInterval(gameInterval.value)
      gameInterval.value = setInterval(gameLoop, 1000 - (level.value - 1) * 100)
    }
  }
}

// 游戏主循环
function gameLoop() {
  if (gameOver.value || isPaused.value) return

  if (!currentPiece.value) {
    currentPiece.value = nextPiece.value
    nextPiece.value = generatePiece()
    if (checkCollision(currentPiece.value, board.value)) {
      gameOver.value = true
      return
    }
  }

  const newPiece = {
    ...currentPiece.value,
    y: currentPiece.value.y + 1
  }

  if (checkCollision(newPiece, board.value)) {
    mergePiece()
    clearLines()
    currentPiece.value = null
  } else {
    currentPiece.value = newPiece
  }
}

// 键盘控制
function handleKeydown(event) {
  if (gameOver.value) {
    if (event.key === 'Enter') {
      resetGame()
    }
    return
  }

  if (event.key === 'p' || event.key === 'P') {
    isPaused.value = !isPaused.value
    return
  }

  if (isPaused.value) return

  if (!currentPiece.value) return

  switch (event.key) {
    case 'ArrowLeft': {
      const newPiece = {
        ...currentPiece.value,
        x: currentPiece.value.x - 1
      }
      if (!checkCollision(newPiece, board.value)) {
        currentPiece.value = newPiece
      }
      break
    }
    case 'ArrowRight': {
      const newPiece = {
        ...currentPiece.value,
        x: currentPiece.value.x + 1
      }
      if (!checkCollision(newPiece, board.value)) {
        currentPiece.value = newPiece
      }
      break
    }
    case 'ArrowDown': {
      const newPiece = {
        ...currentPiece.value,
        y: currentPiece.value.y + 1
      }
      if (!checkCollision(newPiece, board.value)) {
        currentPiece.value = newPiece
      }
      break
    }
    case 'ArrowUp': {
      const rotated = currentPiece.value.shape[0].map((_, i) =>
        currentPiece.value.shape.map(row => row[i]).reverse()
      )
      const newPiece = {
        ...currentPiece.value,
        shape: rotated
      }
      if (!checkCollision(newPiece, board.value)) {
        currentPiece.value = newPiece
      }
      break
    }
  }
}

// 新增：重置游戏
function resetGame() {
  board.value = Array(BOARD_HEIGHT).fill().map(() => Array(BOARD_WIDTH).fill(0))
  score.value = 0
  level.value = 1
  gameOver.value = false
  isPaused.value = false
  currentPiece.value = generatePiece()
  nextPiece.value = generatePiece()
  
  if (gameInterval.value) {
    clearInterval(gameInterval.value)
  }
  gameInterval.value = setInterval(gameLoop, 1000 - (level.value - 1) * 100)
}

// 初始化游戏
onMounted(() => {
  resetGame()
  window.addEventListener('keydown', handleKeydown)
})

onUnmounted(() => {
  if (gameInterval.value) {
    clearInterval(gameInterval.value)
  }
  window.removeEventListener('keydown', handleKeydown)
})
</script>

<template>
  <div class="tetris">
    <div class="game-container">
      <div class="side-panel">
        <div class="next-piece">
          <h3>下一个方块</h3>
          <div class="preview-board">
            <div v-for="(row, y) in 4" :key="y" class="row">
              <div v-for="(col, x) in 4" :key="x" 
                   class="cell"
                   :style="{ backgroundColor: nextPiece && 
                            y < nextPiece.shape.length && 
                            x < nextPiece.shape[0].length && 
                            nextPiece.shape[y][x] ? nextPiece.color : 'transparent' }">
              </div>
            </div>
          </div>
        </div>
        <div class="game-info">
          <h2>得分: {{ score }}</h2>
          <h3>等级: {{ level }}</h3>
          <div v-if="isPaused" class="paused">已暂停</div>
          <div v-if="gameOver" class="game-over">
            游戏结束!
            <div class="restart-hint">按回车键重新开始</div>
          </div>
        </div>
        <div class="controls">
          <h3>操作说明</h3>
          <p>↑ 旋转</p>
          <p>← → 移动</p>
          <p>↓ 加速下落</p>
          <p>P 暂停/继续</p>
        </div>
      </div>
      <div class="game-board" :style="{ width: BOARD_WIDTH * BLOCK_SIZE + 'px' }">
        <div v-for="(row, y) in board" :key="y" class="row">
          <div v-for="(cell, x) in row" :key="x" 
               class="cell" 
               :style="{ backgroundColor: cell || 'transparent' }">
            <div v-if="currentPiece && 
                       y >= currentPiece.y && 
                       y < currentPiece.y + currentPiece.shape.length &&
                       x >= currentPiece.x && 
                       x < currentPiece.x + currentPiece.shape[0].length &&
                       currentPiece.shape[y - currentPiece.y][x - currentPiece.x]"
                 class="current"
                 :style="{ backgroundColor: currentPiece.color }">
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.tetris {
  display: flex;
  justify-content: center;
  padding: 20px;
}

.game-container {
  display: flex;
  gap: 20px;
}

.side-panel {
  display: flex;
  flex-direction: column;
  gap: 20px;
  width: 200px;
}

.preview-board {
  border: 2px solid #333;
  background-color: #f0f0f0;
  padding: 10px;
}

.controls {
  background-color: #f5f5f5;
  padding: 15px;
  border-radius: 8px;
}

.paused {
  color: #ff9800;
  font-size: 24px;
  font-weight: bold;
}

.restart-hint {
  font-size: 16px;
  margin-top: 10px;
  color: #666;
}

.game-info {
  text-align: center;
}

.game-over {
  color: red;
  font-size: 24px;
  font-weight: bold;
}

.game-board {
  border: 2px solid #333;
  background-color: #f0f0f0;
}

.row {
  display: flex;
}

.cell {
  width: 30px;
  height: 30px;
  border: 1px solid #ccc;
  box-sizing: border-box;
}

.cell.filled {
  background-color: #42b883;
}

.current {
  width: 100%;
  height: 100%;
  background-color: #35495e;
}
</style> 