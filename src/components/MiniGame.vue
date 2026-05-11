<template>
  <div class="game-overlay" @click.self="$emit('close')">
    <div class="game-modal">
      <button class="close-game" @click="$emit('close')">✖</button>
      <h2>🧁 Собери кекс! 🧁</h2>
      <p>Нажми на все ингредиенты, чтобы приготовить кекс</p>
      
      <!-- Ингредиенты -->
      <div class="ingredients">
        <button 
          v-for="(item, index) in ingredients" 
          :key="index"
          :class="['ingredient', { collected: item.collected }]"
          @click="collectIngredient(index)"
          :disabled="item.collected || gameFinished"
        >
          {{ item.emoji }} {{ item.name }}
        </button>
      </div>

      <!-- Кекс который собирается -->
      <div class="cake-container">
        <div class="cake-base">
          <span class="cake-emoji">🧁</span>
          <div class="collected-items">
            <span v-for="(item, index) in collectedList" :key="index">
              {{ item.emoji }}
            </span>
          </div>
        </div>
      </div>

      <!-- Прогресс -->
      <div class="progress-bar">
        <div class="progress-fill" :style="{ width: progressPercent + '%' }"></div>
      </div>
      <p>Собрано: {{ collectedCount }} из {{ ingredients.length }}</p>

      <!-- Результат -->
      <div v-if="gameFinished" class="result">
        <h3>🎉 Поздравляю! Кекс готов! 🎉</h3>
        <p>Ты отлично справился!</p>
        <button @click="resetGame" class="play-again">Приготовить ещё</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MiniGame',
  data() {
    return {
      ingredients: [
        { name: 'Мука', emoji: '🌾', collected: false },
        { name: 'Яйцо', emoji: '🥚', collected: false },
        { name: 'Молоко', emoji: '🥛', collected: false },
        { name: 'Сахар', emoji: '🍬', collected: false },
        { name: 'Масло', emoji: '🧈', collected: false }
      ]
    }
  },
  computed: {
    // Количество собранных ингредиентов
    collectedCount() {
      return this.ingredients.filter(item => item.collected).length
    },
    // Процент прогресса
    progressPercent() {
      return (this.collectedCount / this.ingredients.length) * 100
    },
    // Список собранных ингредиентов
    collectedList() {
      return this.ingredients.filter(item => item.collected)
    },
    // Закончена ли игра
    gameFinished() {
      return this.collectedCount === this.ingredients.length
    }
  },
  methods: {
    // Собрать ингредиент
    collectIngredient(index) {
      if (!this.ingredients[index].collected && !this.gameFinished) {
        this.ingredients[index].collected = true
      }
    },
    // Начать заново
    resetGame() {
      this.ingredients.forEach(item => {
        item.collected = false
      })
    }
  }
}
</script>

<style scoped>
/* Затемнение фона */
.game-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

/* Модальное окно */
.game-modal {
  background: linear-gradient(135deg, #fff5e6, #ffe4e1);
  border-radius: 30px;
  padding: 25px;
  max-width: 500px;
  width: 90%;
  position: relative;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

/* Кнопка закрытия */
.close-game {
  position: absolute;
  top: 15px;
  right: 20px;
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #999;
}

.close-game:hover {
  color: #333;
}

/* Заголовок */
h2 {
  color: #d2695a;
  margin-bottom: 10px;
}

/* Ингредиенты */
.ingredients {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: center;
  margin: 20px 0;
}

.ingredient {
  background: white;
  border: 2px solid #ffccaa;
  border-radius: 25px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.3s;
  font-weight:


bold;
}

.ingredient:hover:not(:disabled) {
  transform: scale(1.05);
  background: #ffe4cc;
  border-color: #ff8c5a;
}

.ingredient.collected {
  background: #90ee90;
  border-color: #228b22;
  text-decoration: line-through;
  opacity: 0.7;
  cursor: not-allowed;
}

.ingredient:disabled {
  cursor: not-allowed;
}

/* Контейнер с кексом */
.cake-container {
  background: #f0d8b0;
  border-radius: 20px;
  padding: 20px;
  margin: 20px 0;
  min-height: 150px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.cake-base {
  position: relative;
  text-align: center;
}

.cake-emoji {
  font-size: 80px;
  display: block;
}

.collected-items {
  margin-top: 10px;
  font-size: 24px;
  min-height: 50px;
}

.collected-items span {
  margin: 0 3px;
  animation: bounce 0.5s ease;
}

/* Прогресс-бар */
.progress-bar {
  width: 100%;
  height: 20px;
  background: #e0d0c0;
  border-radius: 10px;
  overflow: hidden;
  margin: 15px 0;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #ff8c5a, #ffd700);
  transition: width 0.3s ease;
  border-radius: 10px;
}

/* Результат */
.result {
  margin-top: 20px;
  padding: 15px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 15px;
}

.result h3 {
  color: #d2695a;
  margin: 0 0 10px 0;
}

.play-again {
  background: #6b4c7a;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 25px;
  cursor: pointer;
  font-size: 16px;
  margin-top: 10px;
}

.play-again:hover {
  background: #8b5a9a;
  transform: scale(1.05);
}

/* Анимации */
@keyframes bounce {
  0% { transform: scale(0); }
  80% { transform: scale(1.2); }
  100% { transform: scale(1); }
}

@keyframes shake {
  0%, 100% { transform: rotate(0deg); }
  25% { transform: rotate(5deg); }
  75% { transform: rotate(-5deg); }
}
</style>