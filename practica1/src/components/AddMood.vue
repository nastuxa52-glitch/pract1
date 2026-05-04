<template>
  <div class="modal-overlay" @click.self="$emit('close')">
    <div class="modal">
      <h2>📝 Как настроение?</h2>
      <button class="close-btn" @click="$emit('close')">✖</button>
      
      <div class="moods-grid">
        <div 
          v-for="mood in moods" 
          :key="mood.type"
          class="mood-card"
          :style="{ background: mood.color + '40', borderColor: mood.color }"
          @click="selectMood(mood)"
        >
          <span class="mood-emoji">{{ mood.emoji }}</span>
          <span class="mood-label">{{ mood.label }}</span>
        </div>
      </div>

      <QuotePopup 
        v-if="showQuote" 
        :mood="selectedMood"
        @close="handleQuoteClose"
      />
    </div>
  </div>
</template>

<script>
import QuotePopup from './QuotePopup.vue'

export default {
  name: 'AddMood',
  components: { QuotePopup },
  props: {
    selectedDate: String
  },
  data() {
    return {
      moods: [
        { type: 'awesome', label: 'Супер', emoji: '🤩', color: '#FFD700' },
        { type: 'good', label: 'Хорошо', emoji: '😊', color: '#98D8C8' },
        { type: 'normal', label: 'Нормально', emoji: '😐', color: '#B0C4DE' },
        { type: 'sad', label: 'Грустно', emoji: '😢', color: '#A8A8C8' },
        { type: 'bad', label: 'Плохо', emoji: '😤', color: '#E8A0A0' }
      ],
      showQuote: false,
      selectedMood: null
    }
  },
  methods: {
    selectMood(mood) {
      this.selectedMood = mood
      this.showQuote = true
    },
    handleQuoteClose() {
      this.showQuote = false
      // Отправляем сохранение, но модалка не закрывается
      this.$emit('save', {
        date: this.selectedDate,
        mood: this.selectedMood.type,
        moodLabel: this.selectedMood.label,
        emoji: this.selectedMood.emoji
      })
      // Модалка остаётся открытой, чтобы можно было добавить ещё эмоцию
    }
  }
}
</script>

<style scoped>
.modal-overlay {
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

.modal {
  background: white;
  border-radius: 30px;
  padding: 30px;
  min-width: 300px;
  position: relative;
  text-align: center;
}

.close-btn {
  position: absolute;
  top: 15px;
  right: 15px;
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
}

.moods-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 15px;
  margin-top: 20px;
}

.mood-card {
  padding: 20px;
  border-radius: 20px;
  border: 2px solid;
  cursor: pointer;
  transition: transform 0.2s;
  text-align: center;
}

.mood-card:hover {
  transform: scale(1.05);
}

.mood-emoji {
  font-size: 40px;
  display: block;
}

.mood-label {
  margin-top: 10px;
  font-weight: bold;
}
</style>