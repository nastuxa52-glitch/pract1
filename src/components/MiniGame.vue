<template>
  <div class="game-overlay" @click.self="$emit('close')">
    <div class="game-modal">
      <button class="close-game" @click="$emit('close')">✖</button>
      <h2>🍰 Лови украшения!</h2>
      
      <div class="game-stats">
        <span>⭐ {{ score }}</span>
        <span>❤️ {{ lives }}</span>
      </div>

      <div class="game-area" @mousemove="moveCatcher">
        <div class="catcher" :style="{ left: catcherX + 'px' }">🧁</div>
        <div v-for="(item, i) in items" :key="i" class="item" :style="{ left: item.x + 'px', top: item.y + 'px' }">
          {{ item.e }}
        </div>
      </div>

      <div v-if="!active">
        <p>Игра окончена! Счёт: {{ score }}</p>
        <button @click="start" class="olive-btn">Играть снова</button>
      </div>
      <button v-else @click="start" class="olive-btn">Начать заново</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      score: 0,
      lives: 3,
      catcherX: 150,
      items: [],
      active: true,
      timer: null
    }
  },
  methods: {
    moveCatcher(e) {
      if (!this.active) return
      const rect = document.querySelector('.game-area').getBoundingClientRect()
      let x = e.clientX - rect.left - 30
      if (x < 0) x = 0
      if (x > rect.width - 60) x = rect.width - 60
      this.catcherX = x
    },
    update() {
      if (!this.active) return
      for (let i = 0; i < this.items.length; i++) {
        this.items[i].y += 2
        if (this.items[i].y > 190 && Math.abs(this.items[i].x - this.catcherX) < 45) {
          this.items.splice(i, 1)
          this.score++
          i--
        } else if (this.items[i].y > 260) {
          this.items.splice(i, 1)
          this.lives--
          i--
          if (this.lives === 0) this.end()
        }
      }
      if (Math.random() < 0.15) {
        this.items.push({ e: ['🍫', '🍬', '🍒', '🍡'][Math.floor(Math.random() * 4)], x: Math.random() * 300, y: 0 })
      }
    },
    start() {
      this.score = 0
      this.lives = 3
      this.items = []
      this.active = true
      if (this.timer) clearInterval(this.timer)
      this.timer = setInterval(() => this.update(), 50)
    },
    end() {
      this.active = false
      clearInterval(this.timer)
    }
  },
  mounted() { this.start() },
  beforeDestroy() { clearInterval(this.timer) }
}
</script>

<style scoped>
/* Затемнение фона */
.game-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0,0,0,0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

/* ГЛАВНОЕ ОКНО ИГРЫ — ГОЛУБОЙ ФОН */
.game-modal {
  background: #87CEEB;  /* небесно-голубой */
  border-radius: 30px;
  padding: 20px;
  max-width: 400px;
  width: 90%;
  text-align: center;
  box-shadow: 0 10px 25px rgba(0,0,0,0.2);
  position: relative;
}

.close-game {
  position: absolute;
  top: 10px;
  right: 12px;
  background: white;
  border: none;
  font-size: 18px;
  border-radius: 50%;
  width: 28px;
  height: 28px;
  cursor: pointer;
}

.game-stats {
  display: flex;
  justify-content: space-between;
  background: rgba(255,255,255,0.8);
  padding: 8px 15px;
  border-radius: 20px;
  margin: 10px 0;
  font-weight: bold;
}

.game-area {
  position: relative;
  height: 280px;
  background: #B0E0FF;  /* светло-голубое игровое поле */
  border-radius: 20px;
  margin: 10px 0;
  overflow: hidden;
  cursor: none;
}

.catcher {
  position: absolute;
  bottom: 10px;
  font-size: 45px;
  transition: left 0.05s;
}

.item {
  position: absolute;
  font-size: 28px;
}

/* ОЛИВКОВАЯ КНОПКА */
.olive-btn {
  background: #6B8E23;
  color: white;
  border: none;
  padding: 8px 18px;
  border-radius: 25px;
  font-size: 14px;
  font-weight: bold;
  cursor: pointer;
  margin: 10px 0 5px;
}

.olive-btn:hover {
  background: #556B2F;
}

.game-over div {
  margin: 5px 0;
}
</style>