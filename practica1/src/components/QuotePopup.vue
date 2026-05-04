<template>
  <div class="quote-overlay" @click.self="$emit('close')">
    <div class="quote-modal" :style="{ borderTop: `5px solid ${mood.color}` }">
      <p class="quote-text">"{{ quote.text }}"</p>
      <p class="quote-author">— {{ quote.author }}</p>
      <button @click="$emit('close')" class="ok-btn">Спасибо, запомню ✨</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'QuotePopup',
  props: {
    mood: {
      type: Object,
      required: true
    }
  },
  computed: {
    quote() {
      const quotesByMood = {
        awesome: [
          { text: 'Твоя энергия заражает всех вокруг!', author: 'Вселенная' },
          { text: 'Продолжай в том же духе, ты на высоте!', author: 'Твой внутренний голос' }
        ],
        good: [
          { text: 'Хороший день — отличная основа для лучшего завтра!', author: 'Мудрец' },
          { text: 'Сохрани этот позитив, он пригодится', author: 'Твой ангел-хранитель' }
        ],
        normal: [
          { text: 'Иногда нормально — это тоже хорошо. Отдохни немного.', author: 'Совет' },
          { text: 'Сделай маленький шаг к чему-то приятному для себя', author: 'Мотиватор' }
        ],
        sad: [
          { text: 'Грусть — это нормально. Завтра будет новый день 🌅', author: 'Поддержка' },
          { text: 'Ты сильнее, чем думаешь. Выпей чай и отдохни', author: 'Твой друг' }
        ],
        bad: [
          { text: 'Плохие дни бывают у всех. Но они проходят. Ты справишься! 💪', author: 'Мотивация' },
          { text: 'Возьми паузу, дыши. Уже скоро всё наладится', author: 'Спокойствие' }
        ]
      }
      const quotes = quotesByMood[this.mood.type] || quotesByMood.normal
      return quotes[Math.floor(Math.random() * quotes.length)]
    }
  }
}
</script>

<style scoped>
.quote-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0,0,0,0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1100;
}

.quote-modal {
  background: white;
  border-radius: 30px;
  padding: 30px;
  max-width: 350px;
  text-align: center;
  animation: bounce 0.3s ease;
}

.quote-text {
  font-size: 1.2rem;
  font-style: italic;
  margin-bottom: 15px;
  color: #333;
}

.quote-author {
  color: #888;
  margin-bottom: 20px;
}

.ok-btn {
  background: #6b4c7a;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 25px;
  cursor: pointer;
}

@keyframes bounce {
  0% { transform: scale(0.8); opacity: 0; }
  100% { transform: scale(1); opacity: 1; }
}
</style>