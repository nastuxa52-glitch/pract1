<template>
  <div class="game-overlay" @click.self="$emit('close')">
    <div class="game-modal">
      <button class="close-game" @click="$emit('close')">✖</button>
      <h2>🍰 Лови украшения для кекса!</h2>
      
      <div class="game-stats">
        <span>⭐ Счёт: {{ score }}</span>
        <span>❤️ Жизни: {{ lives }}</span>
      </div>

      <div class="game-area" @mousemove="moveCatcher" @touchmove="moveCatcher">
        <!-- Ловушка (кекс внизу) -->
        <div class="cake-catcher" :style="{ left: catcherX + 'px' }">
          🧁
        </div>
        
        <!-- Все падающие предметы -->
        <div v-for="(item, index) in fallingItems" :key="index" 
             class="falling-item"
             :style="{ left: item.x + 'px', top: item.y + 'px' }">
          {{ item.emoji }}
        </div>
      </div>

      <!-- Экран "Игра окончена" -->
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
      score: 0,           // очки
      lives: 3,           // жизни
      catcherX: 150,      // позиция кекса (по горизонтали)
      fallingItems: [],   // массив падающих предметов
      gameActive: true,   // игра активна?
      gameInterval: null  // таймер для обновления
    }
  },
  computed: {
    gameOver() {
      return !this.gameActive
    }
  },
  methods: {
    // Движение кекса за мышкой
    moveCatcher(e) {
      if (!this.gameActive) return
      
      // Находим игровую область
      const rect = document.querySelector('.game-area').getBoundingClientRect()
      // Вычисляем позицию мыши
      let x = (e.clientX || e.touches?.[0]?.clientX) - rect.left
      // Отнимаем половину ширины кекса (40 пикселей)
      x = x - 30
      // Ограничиваем, чтобы кекс не вылезал за края
      if (x < 0) x = 0
      if (x > rect.width - 60) x = rect.width - 60
      // Сохраняем позицию
      this.catcherX = x
    },
    
    // Добавить новый падающий предмет
    addFallingItem() {
      if (!this.gameActive) return
      
      // Список украшений (конфетки, украшения)
      const items = ['🍫', '🍬', '🍒', '🎊', '🍡', '🍭', '🍩']
      // Выбираем случайное украшение
      const randomItem = items[Math.floor(Math.random() * items.length)]
      // Случайная позиция по горизонтали
      const randomX = Math.random() * 300
      
      // Добавляем в массив
      this.fallingItems.push({
        emoji: randomItem,
        x: randomX,
        y: 0  // начинаем с верха
      })
    },
    
    // Обновление игры (вызывается много раз в секунду)
    updateGame() {
      if (!this.gameActive) return
      
      // 1. Двигаем все предметы вниз
      for (let i = 0; i < this.fallingItems.length; i++) {
        const item = this.fallingItems[i]
        // Скорость падения - 2 пикселя за раз (было 5, стало 2 - медленнее!)
        item.y += 2
        
        // 2. Проверка: поймали ли предмет?
        // Предмет пойман, если:
        // - он внизу (y > 190)
        // - он на одной высоте с кексом
        // - они рядом по горизонтали
        if (item.y > 190 && item.y < 230 && Math.abs(item.x - this.catcherX) < 45) {
          // Удаляем предмет
          this.fallingItems.splice(i, 1)
          // Увеличиваем счёт
          this.score++
          i--  // потому что массив уменьшился
        }
        // 3. Проверка: предмет упал на землю?
        else if (item.y > 260) {
          // Удаляем предмет
          this.fallingItems.splice(i, 1)
          // Отнимаем жизнь
          this.lives--
          i--
          
          // Если жизни кончились - игра окончена
          if (this.lives <= 0) {
            this.gameActive = false
            clearInterval(this.gameInterval)  // останавливаем таймер
          }
        }
      }
      
      // 4. Иногда добавляем новый предмет (чем реже, тем легче)
      // 0.15 = 15% шанс каждый раз (было 0.3 - стало реже)
      if (Math.random() < 0.15) {
        this.addFallingItem()
      }
    },
    
    // Начать игру (или начать заново)
    startGame() {
      // Сбрасываем все значения
      this.score = 0
      this.lives = 3
      this.fallingItems = []
      this.gameActive = true
      this.catcherX = 150
      
      // Останавливаем старый таймер, если был
      if (this.gameInterval) clearInterval(this.gameInterval)
      // Запускаем новый таймер (каждые 50 миллисекунд)
      this.gameInterval = setInterval(() => this.updateGame(), 50)
    }
  },
  // Когда игра открывается - запускаем её
  mounted() {
    this.startGame()
  },
  // Когда игра закрывается - выключаем таймер
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
  background: rgba(0,0,0,0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.game-modal {
  background: linear-gradient(135deg, #fff5e6, #ffe4e1);
  border-radius: 30px;
  padding: 20px;
  max-width: 450px;
  width: 90%;
  position: relative;
  text-align: center;
}

.close-game {
  position: absolute;
  top: 10px;
  right: 10px;
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
}

.game-stats {
  display: flex;
  justify-content: space-between;
  padding: 10px 15px;
  background: #fff0e0;
  border-radius: 15px;
  margin: 10px 0;
  font-weight: bold;
}

.game-area {
  position: relative;
  height: 300px;
  background: #f5e6d3;
  border-radius: 20px;
  margin: 15px 0;
  overflow: hidden;
  cursor: none;
}

.cake-catcher {
  position: absolute;
  bottom: 15px;
  font-size: 50px;
  transition: left 0.05s linear;
  pointer-events: none;
  filter: drop-shadow(2px 2px 4px rgba(0,0,0,0.2));
}

.falling-item {
  position: absolute;
  font-size: 30px;
  pointer-events: none;
}

.game-over {
  margin-top: 15px;
  padding: 15px;
  background: rgba(255,255,255,0.9);
  border-radius: 15px;
}

.game-over div {
  margin: 8px 0;
  font-size: 18px;
  font-weight: bold;
}

.reset-btn, .again-btn {
  background: #ff8c5a;
  color: white;
  border: none;
  padding: 8px 20px;
  border-radius: 25px;
  cursor: pointer;
  margin-top: 10px;
  font-size: 14px;
}

.reset-btn:hover, .again-btn:hover {
  background: #ff6b3a;
}
</style>