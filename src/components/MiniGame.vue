<template>
  <div class="game-overlay" @click.self="$emit('close')">
    <div class="game-modal">
      <button class="close-game" @click="$emit('close')">✖</button>
      <h2>🍰 Лови украшения для кекса</h2>
      
      <div class="game-stats">
        <span>⭐ Счёт: {{ score }}</span>
        <span>❤️ Жизни: {{ lives }}</span>
      </div>

      <div class="game-area" @mousemove="moveCatcher" @touchmove="moveCatcher">
        <div class="cake-catcher" :style="{ left: catcherX + 'px' }">
          🧁
        </div>
        <div v-for="(item, index) in fallingItems" :key="index" 
             class="falling-item"
             :style="{ left: item.x + 'px', top: item.y + 'px' }">
          {{ item.emoji }}
        </div>
      </div>

      <div v-if="gameOver" class="game-over">
        <div>🎮 Игра окончена!</div>
        <div>Твой счёт: {{ score }}</div>
        <button @click="startGame" class="again-btn">Играть снова</button>
      </div>
      
      <button v-else @click="startGame" class="reset-btn">🔄 Начать заново</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MiniGame',
  data() {
    return {
      score: 0,
      lives: 3,
      catcherX: 150,
      fallingItems: [],
      gameActive: true,
      gameInterval: null
    }
  },
  computed: {
    gameOver() {
      return !this.gameActive
    }
  },
  methods: {
    moveCatcher(e) {
      if (!this.gameActive) return
      const rect = document.querySelector('.game-area').getBoundingClientRect()
      let x = (e.clientX || e.touches?.[0]?.clientX) - rect.left - 30
      if (x < 0) x = 0
      if (x > rect.width - 60) x = rect.width - 60
      this.catcherX = x
    },
    addFallingItem() {
      if (!this.gameActive) return
      const items = ['🍫', '🍬', '🍒', '🎊', '🍡', '🍭', '🍩']
      this.fallingItems.push({
        emoji: items[Math.floor(Math.random() * items.length)],
        x: Math.random() * 300,
        y: 0
      })
    },
    updateGame() {
      if (!this.gameActive) return
      for (let i = 0; i < this.fallingItems.length; i++) {
        this.fallingItems[i].y += 2
        if (this.fallingItems[i].y > 190 && this.fallingItems[i].y < 230 && 
            Math.abs(this.fallingItems[i].x - this.catcherX) < 45) {
          this.fallingItems.splice(i, 1)
          this.score++
          i--
        } else if (this.fallingItems[i].y > 260) {
          this.fallingItems.splice(i, 1)
          this.lives--
          i--
          if (this.lives <= 0) {
            this.gameActive = false
            clearInterval(this.gameInterval)
          }
        }
      }
      if (Math.random() < 0.15) {
        this.addFallingItem()
      }
    },
    startGame() {
      this.score = 0
      this.lives = 3
      this.fallingItems = []
      this.gameActive = true
      this.catcherX = 150
      if (this.gameInterval) clearInterval(this.gameInterval)
      this.gameInterval = setInterval(() => this.updateGame(), 50)
    }
  },
  mounted() {
    this.startGame()
  },
  beforeDestroy() {
    if (this.gameInterval) clearInterval(this.gameInterval)
  }
}
</script>

<style scoped>
.game-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0,0,0,0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

/* ФОН ОКНА ИГРЫ — КАК ФОН КАЛЕНДАРЯ (светлый, пастельный) */
.game-modal {
  background: #e8a0a0;
  border-radius: 30px;
  padding: 20px;
  max-width: 450px;
  width: 90%;
  position: relative;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0,0,0,0.15);
  border: 1px solid #e8a0a0;
}


.game-modal h2 {
  color: white;
  background: #e8a0a0;
  display: inline-block;
  padding: 8px 20px;
  border-radius: 40px;
  font-size: 24px;
  margin: 5px 0 10px;
}

.close-game {
  position: absolute;
  top: 10px;
  right: 10px;
  background: #e8a0a0;
  border: none;
  font-size: 18px;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  cursor: pointer;
}

.close-game:hover {
  background: #e8a0a0;
}

.game-stats {
  display: flex;
  justify-content: space-between;
  padding: 10px 15px;
  background: #F5EDE0;
  border-radius: 15px;
  margin: 10px 0;
  font-weight: bold;
  color: #5A4A3A;
}

/* ИГРОВАЯ ОБЛАСТЬ — НЕЖНО-ГОЛУБАЯ (как фон календаря) */
.game-area {
  position: relative;
  height: 300px;
  background: #E8F0F5;
  border-radius: 20px;
  margin: 15px 0;
  overflow: hidden;
  cursor: none;
  border: 1px solid #D4E0E8;
}

.cake-catcher {
  position: absolute;
  bottom: 15px;
  font-size: 50px;
  transition: left 0.05s linear;
  pointer-events: none;
  filter: drop-shadow(2px 2px 4px rgba(0,0,0,0.1));
}

.falling-item {
  position: absolute;
  font-size: 30px;
  pointer-events: none;
}

.game-over {
  margin-top: 15px;
  padding: 15px;
  background: #F5EDE0;
  border-radius: 15px;
}

.game-over div {
  margin: 8px 0;
  font-size: 18px;
  font-weight: bold;
  color: #5A4A3A;
}

/* КНОПКА НАЧАТЬ ЗАНОВО */
.reset-btn {
  background: #a8a8c8;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 25px;
  cursor: pointer;
  margin-top: 10px;
  font-size: 16px;
  font-weight: bold;
  transition: 0.2s;
}

.reset-btn:hover {
  background: #a8a8c8;
}

.again-btn {
  background: #a8a8c8;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 25px;
  cursor: pointer;
  margin-top: 10px;
  font-size: 16px;
  font-weight: bold;
}

.again-btn:hover {
  background: #a8a8c8;
}
</style>