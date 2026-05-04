<template>
  <div class="game-overlay" @click.self="$emit('close')">
    <div class="game-modal">
      <button class="close-game" @click="$emit('close')">✖</button>
      <h2>🧁 Готовим кекс, чтобы поднять настроение!</h2>
      
      <div class="game-content">
        <div class="ingredients">
          <h3>Ингредиенты:</h3>
          <div class="ingredients-list">
            <div 
              v-for="ing in ingredients" 
              :key="ing.name"
              class="ingredient"
              :class="{ used: ing.used }"
              @click="useIngredient(ing)"
            >
              {{ ing.emoji }} {{ ing.name }}
            </div>
          </div>
        </div>

        <div class="bowl" :class="{ mixing: isMixing }">
          <div class="bowl-content">
            <span v-if="usedIngredients.length === 0">🥣</span>
            <span v-else>{{ usedIngredients.map(i => i.emoji).join(' ') }}</span>
          </div>
        </div>

        <button 
          v-if="usedIngredients.length === ingredients.length && !cakeReady"
          @click="bakeCake"
          class="bake-btn"
        >
          🎂 Испечь кекс!
        </button>

        <div v-if="cakeReady" class="result">
          <div class="cake">🧁✨ ГОТОВО! ✨🧁</div>
          <p>Твой кекс поднял настроение на +100%!</p>
          <button @click="playAgain" class="again-btn">Приготовить ещё</button>
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
      ingredients: [
        { name: 'Мука', emoji: '🌾', used: false },
        { name: 'Яйцо', emoji: '🥚', used: false },
        { name: 'Молоко', emoji: '🥛', used: false },
        { name: 'Сахар', emoji: '🍬', used: false },
        { name: 'Масло', emoji: '🧈', used: false }
      ],
      usedIngredients: [],
      isMixing: false,
      cakeReady: false
    }
  },
  methods: {
    useIngredient(ing) {
      if (!ing.used && !this.cakeReady) {
        ing.used = true
        this.usedIngredients.push(ing)
        this.isMixing = true
        setTimeout(() => { this.isMixing = false }, 300)
      }
    },
    bakeCake() {
      this.isMixing = true
      setTimeout(() => {
        this.isMixing = false
        this.cakeReady = true
      }, 1000)
    },
    playAgain() {
      this.ingredients.forEach(i => i.used = false)
      this.usedIngredients = []
      this.cakeReady = false
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

.ingredients-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
  margin: 15px 0;
}

.ingredient {
  background: #fff;
  padding: 8px 15px;
  border-radius: 25px;
  cursor: pointer;
  transition: 0.2s;
  border: 1px solid #ffccaa;
}

.ingredient:hover {
  transform: scale(1.05);
  background: #ffe4cc;
}

.ingredient.used {
  opacity: 0.5;
  text-decoration: line-through;
  cursor: not-allowed;
}

.bowl {
  background: #e8d8c0;
  border-radius: 50% 50% 30% 30%;
  min-height: 150px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 20px 0;
  transition: 0.1s;
}

.bowl.mixing {
  animation: shake 0.3s ease-in-out;
}

.bowl-content {
  font-size: 40px;
}

@keyframes shake {
  0%, 100% { transform: rotate(0deg); }
  25% { transform: rotate(5deg); }
  75% { transform: rotate(-5deg); }
}

.bake-btn {
  background: #ff8c5a;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 30px;
  font-size: 18px;
  cursor: pointer;
}

.result {
  margin-top: 20px;
}

.cake {
  font-size: 24px;
  font-weight: bold;
  animation: bounce 0.5s ease;
}

.again-btn {
  background: #6b4c7a;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 25px;
  cursor: pointer;
  margin-top: 15px;
}
</style>