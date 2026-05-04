<template>
  <div class="game-overlay" @click.self="$emit('close')">
    <div class="game-modal">
      <button class="close-game" @click="$emit('close')">✖</button>
      <h2>🎨 Украшаем кексик, чтобы поднять настроение!</h2>
      
      <div class="game-content">
        <div class="decorations">
          <h3>Украшения:</h3>
          <div class="decorations-list">
            <div 
              v-for="dec in decorations" 
              :key="dec.name"
              class="decoration"
              :class="{ used: dec.used }"
              @click="useDecoration(dec)"
            >
              {{ dec.emoji }} {{ dec.name }}
            </div>
          </div>
        </div>

        <div class="cake-base" :class="{ decorating: isDecorating }">
          <div class="cake-content">
            <div class="base-cake">🧁</div>
            <div class="toppings">
              <span v-for="(dec, index) in usedDecorations" :key="index" class="topping">
                {{ dec.emoji }}
              </span>
            </div>
          </div>
        </div>

        <div v-if="cakeFinished" class="result">
          <div class="cake-finished">🎨✨ ШЕДЕВР! ✨🎨</div>
          <p>Твой украшенный кекс поднял настроение на +100%!</p>
          <button @click="playAgain" class="again-btn">Украсить новый кекс</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MiniGame',
  data() {
    return {
      decorations: [
        { name: 'Посыпка', emoji: '🌈', used: false },
        { name: 'Глазурь', emoji: '🍫', used: false },
        { name: 'Вишенка', emoji: '🍒', used: false },
        { name: 'Конфетти', emoji: '🎊', used: false },
        { name: 'Орешки', emoji: '🥜', used: false },
        { name: 'Маршмеллоу', emoji: '🍡', used: false }
      ],
      usedDecorations: [],
      isDecorating: false,
      cakeFinished: false
    }
  },
  methods: {
    useDecoration(dec) {
      if (!dec.used && !this.cakeFinished) {
        dec.used = true
        this.usedDecorations.push(dec)
        this.isDecorating = true
        
        setTimeout(() => { 
          this.isDecorating = false 
        }, 200)
        
        // Проверяем, все ли украшения использованы
        setTimeout(() => {
          if (this.decorations.every(d => d.used === true)) {
            this.cakeFinished = true
          }
        }, 300)
      }
    },
    playAgain() {
      this.decorations.forEach(d => d.used = false)
      this.usedDecorations = []
      this.cakeFinished = false
    }
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
  background: linear-gradient(135deg, #fff5e6 0%, #ffe4e1 100%);
  border-radius: 30px;
  padding: 25px;
  max-width: 500px;
  width: 90%;
  position: relative;
  text-align: center;
}

.close-game {
  position: absolute;
  top: 15px;
  right: 15px;
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
}

.decorations-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
  margin: 15px 0;
}

.decoration {
  background: #fff;
  padding: 8px 15px;
  border-radius: 25px;
  cursor: pointer;
  transition: 0.2s;
  border: 1px solid #ffccaa;
}

.decoration:hover {
  transform: scale(1.05);
  background: #ffe4cc;
}

.decoration.used {
  opacity: 0.5;
  text-decoration: line-through;
  cursor: not-allowed;
  filter: grayscale(0.3);
}

.cake-base {
  background: #f0d8b0;
  border-radius: 30px;
  min-height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 20px 0;
  position: relative;
  transition: 0.1s;
}

.cake-base.decorating {
  animation: bounce 0.3s ease;
}

.cake-content {
  position: relative;
  text-align:


center;
}

.base-cake {
  font-size: 80px;
  display: inline-block;
  position: relative;
}

.toppings {
  position: absolute;
  top: 15%;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 5px;
  font-size: 30px;
  white-space: nowrap;
  animation: float 0.3s ease;
}

.topping {
  display: inline-block;
}

@keyframes bounce {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.03); }
}

@keyframes float {
  0% { opacity: 0; transform: translateX(-50%) translateY(20px); }
  100% { opacity: 1; transform: translateX(-50%) translateY(0); }
}

.result {
  margin-top: 20px;
}

.cake-finished {
  font-size: 24px;
  font-weight: bold;
  animation: bounce 0.5s ease;
  background: linear-gradient(45deg, #ff6b3a, #ffd700);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.again-btn {
  background: #6b4c7a;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 25px;
  cursor: pointer;
  margin-top: 15px;
  transition: 0.2s;
}

.again-btn:hover {
  background: #8b5a9a;
  transform: scale(1.05);
}
</style>