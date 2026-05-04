<template>
  <div class="calendar">
    <div class="header">
      <button @click="prevMonth">◀</button>
      <h2>{{ monthName }} {{ year }}</h2>
      <button @click="nextMonth">▶</button>
    </div>

    <div class="weekdays">
      <span v-for="d in ['Пн','Вт','Ср','Чт','Пт','Сб','Вс']" :key="d">{{ d }}</span>
    </div>

    <div class="days">
      <div 
        v-for="day in days" 
        :key="day.date"
        class="day"
        :style="{ background: getColor(day.date) }"
        @click="openMood(day.date)"
      >
        {{ day.day }}
      </div>
    </div>

    <div class="chart">
      <div v-for="n in 30" :key="n" class="bar" :style="{ height: getAvg(n) * 20 + '%', background: getBarColor(getAvg(n)) }"></div>
    </div>

    <!-- Модалка выбора эмоции -->
    <div v-if="modal" class="modal" @click.self="modal = false">
      <div class="modal-box">
        <h3>{{ selectedDate }}</h3>
        <div class="emojis">
          <span v-for="(m, key) in moods" :key="key" @click="save(key)" class="emoji">{{ m.emoji }}</span>
        </div>
        <button @click="modal = false">Закрыть</button>
      </div>
    </div>

    <!-- Модалка цитаты -->
    <div v-if="quoteModal" class="modal" @click.self="quoteModal = false">
      <div class="modal-box">
        <h3>✨ Цитата ✨</h3>
        <p class="quote">{{ quoteText }}</p>
        <button @click="quoteModal = false">Спасибо</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      date: new Date(),
      moods: {
        awesome: { emoji: '🤩', score: 5, color: '#FFD700' },
        good: { emoji: '😊', score: 4, color: '#98D8C8' },
        normal: { emoji: '😐', score: 3, color: '#B0C4DE' },
        sad: { emoji: '😢', score: 2, color: '#A8A8C8' },
        bad: { emoji: '😤', score: 1, color: '#E8A0A0' }
      },
      entries: [],
      modal: false,
      quoteModal: false,
      selectedDate: '',
      quoteText: ''
    }
  },
  computed: {
    year() { return this.date.getFullYear() },
    month() { return this.date.getMonth() },
    monthName() { 
      return ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь'][this.month] 
    },
    days() {
      const daysInMonth = new Date(this.year, this.month + 1, 0).getDate()
      const firstDay = new Date(this.year, this.month, 1).getDay() || 7
      const days = []
      for (let i = 1; i < firstDay; i++) days.push({ date: '', day: '' })
      for (let i = 1; i <= daysInMonth; i++) {
        days.push({ date: `${this.year}-${this.month+1}-${i}`, day: i })
      }
      return days
    }
  },
  mounted() {
    const saved = localStorage.getItem('entries')
    if (saved) this.entries = JSON.parse(saved)
  },
  methods: {
    saveEntries() { localStorage.setItem('entries', JSON.stringify(this.entries)) },
    getColor(date) {
      const dayEntries = this.entries.filter(e => e.date === date)
      if (!dayEntries.length) return '#f0f0f0'
      const avg = dayEntries.reduce((s,e) => s + e.score, 0) / dayEntries.length
      if (avg >= 4.5) return '#FFD700'
      if (avg >= 3.5) return '#98D8C8'
      if (avg >= 2.5) return '#B0C4DE'
      if (avg >= 1.5) return '#A8A8C8'
      return '#E8A0A0'
    },
    getBarColor(score) {
      if (score >= 4.5) return '#FFD700'
      if (score >= 3.5) return '#98D8C8'
      if (score >= 2.5) return '#B0C4DE'
      if (score >= 1.5) return '#A8A8C8'
      return '#E8A0A0'
    },
    getAvg(day) {
      const date = `${this.year}-${this.month+1}-${day}`
      const dayEntries = this.entries.filter(e => e.date === date)
      if (!dayEntries.length) return 0
      return dayEntries.reduce((s,e) => s + e.score, 0) / dayEntries.length
    },
    prevMonth() { this.date = new Date(this.year, this.month - 1) },
    nextMonth() { this.date = new Date(this.year, this.month + 1) },
    openMood(date) { if (date) { this.selectedDate = date; this.modal = true } },
    save(type) {
      this.entries.push({ 
        date: this.selectedDate, 
        score: this.moods[type].score, 
        emoji: this.moods[type].emoji, 
        time: Date.now() 
      })
      this.saveEntries()
      
      const quotes = { 
        awesome: [
          '✨ Ты супер! Сияй дальше! ✨',
          '💫 Твоя энергия заражает всех! 💫',
          '🌟 Так держать, ты на высоте! 🌟'
        ],
        good: [
          '🌸 Отличный день! Продолжай! 🌸',
          '💪 Ты молодец, так держать! 💪',
          '🎉 Позитив — твоя суперсила! 🎉'
        ],
        normal: [
          '🌿 Нормально — уже хорошо! 🌿',
          '🌈 Завтра будет ещё лучше! 🌈',
          '🧘 Спокойствие — это сила! 🧘'
        ],
        sad: [
          '🌧️ Грусть пройдёт, солнце выглянет 🌈',
          '💙 Ты справишься, дай себе время 💙',
          '🦋 Всё наладится! 🦋'
        ],
        bad: [
          '🌊 Плохой день пройдёт 🌊',
          '🌺 Ты сильнее, чем кажется! 🌺',
          '💜 Ты справишься, верю в тебя! 💜'
        ]
      }
      const list = quotes[type] || quotes.normal
      this.quoteText = list[Math.floor(Math.random() * list.length)]
      this.quoteModal = true
      this.modal = false
    }
  }
}
</script>

<style scoped>
.calendar { max-width: 500px; margin: 20px auto; background: #e8f4f8; border-radius: 20px; padding: 20px; }
.header { display: flex; justify-content: space-between; align-items: center; }
.header button { background: #4a9ebf; color: white; border: none; width: 35px; height: 35px; border-radius: 50%; cursor: pointer; }
.header h2 { color: #2c5f7a; }
.weekdays { display: grid; grid-template-columns: repeat(7,1fr); text-align: center; margin: 10px 0; color: #2c5f7a; font-weight: bold; }
.days { display: grid; grid-template-columns: repeat(7,1fr); gap: 5px; }
.day { aspect-ratio: 1; display: flex; align-items: center; justify-content: center; border-radius: 10px; cursor: pointer; font-weight: bold; color: #333; transition: 0.2s; }
.day:hover { transform: scale(1.05); }
.chart { display: flex; gap: 2px; height: 100px; align-items: flex-end; margin-top: 20px; }
.bar { flex: 1; min-height: 2px; border-radius: 3px 3px 0 0; }
.modal { position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.5); display: flex; align-items: center; justify-content: center; z-index: 1000; }
.modal-box { background: white; padding: 25px; border-radius: 25px; text-align: center; min-width: 280px; }
.emojis { display: flex; gap: 15px; justify-content: center; margin: 15px 0; }
.emoji { font-size: 40px; cursor: pointer; transition: 0.2s; }
.emoji:hover { transform: scale(1.2); }
.quote { font-size: 1.2rem; color: #4a9ebf; margin: 15px 0; font-weight: bold; }
button { background: #4a9ebf; color: white; border: none; padding: 8px 20px; border-radius: 20px; cursor: pointer; }
</style>