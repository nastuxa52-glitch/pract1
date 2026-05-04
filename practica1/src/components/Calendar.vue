<template>
  <div class="calendar">
    <!-- Шапка с переключением месяцев -->
    <div class="calendar-header">
      <button @click="prevMonth">◀</button>
      <h2>{{ monthName }} {{ year }}</h2>
      <button @click="nextMonth">▶</button>
    </div>

    <!-- Дни недели -->
    <div class="weekdays">
      <span v-for="d in ['Пн','Вт','Ср','Чт','Пт','Сб','Вс']" :key="d">{{ d }}</span>
    </div>

    <!-- Календарь -->
    <div class="calendar-days">
      <div 
        v-for="day in days" 
        :key="day.date"
        class="day"
        :style="{ background: getDayColor(day.date) }"
        @click="openMood(day.date)"
      >
        {{ day.day }}
      </div>
    </div>

    <!-- Простой график -->
    <div class="chart">
      <h3>График настроения🌸</h3>
      <div class="chart-bars">
        <div v-for="n in 30" :key="n" class="chart-bar" :style="{ height: getAvgScore(n) * 30 + '%' }"></div>
      </div>
    </div>

    <!-- Модалка для выбора эмоции -->
    <div v-if="modalOpen" class="modal" @click.self="modalOpen = false">
      <div class="modal-content">
        <h3>{{ selectedDate }}</h3>
        <div class="emojis">
          <span v-for="m in moods" :key="m.type" @click="saveMood(m.type)" class="emoji">
            {{ m.emoji }}
          </span>
        </div>
        <p v-if="quote" class="quote">✨ {{ quote }}</p>
        <button @click="modalOpen = false">Закрыть</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentDate: new Date(),
      moods: {
        awesome: { emoji: '🤩', score: 5, color: '#FFD700' },
        good: { emoji: '😊', score: 4, color: '#98D8C8' },
        normal: { emoji: '😐', score: 3, color: '#B0C4DE' },
        sad: { emoji: '😢', score: 2, color: '#A8A8C8' },
        bad: { emoji: '😤', score: 1, color: '#E8A0A0' }
      },
      entries: [],
      modalOpen: false,
      selectedDate: '',
      quote: ''
    }
  },
  computed: {
    year() { return this.currentDate.getFullYear() },
    month() { return this.currentDate.getMonth() },
    monthName() { return ['Янв','Фев','Мар','Апр','Май','Июн','Июл','Авг','Сен','Окт','Ноя','Дек'][this.month] },
    days() {
      const firstDay = new Date(this.year, this.month, 1)
      const start = firstDay.getDay() || 7
      const daysInMonth = new Date(this.year, this.month + 1, 0).getDate()
      const days = []
      for (let i = 1; i <= daysInMonth; i++) {
        days.push({ date: `${this.year}-${this.month+1}-${i}`, day: i })
      }
      return days
    }
  },
  mounted() {
    const saved = localStorage.getItem('moodEntries')
    if (saved) this.entries = JSON.parse(saved)
  },
  methods: {
    saveEntries() { localStorage.setItem('moodEntries', JSON.stringify(this.entries)) },
    getDayColor(dateStr) {
      const dayEntries = this.entries.filter(e => e.date === dateStr)
      if (dayEntries.length === 0) return '#eee'
      const avg = dayEntries.reduce((s,e) => s + e.score, 0) / dayEntries.length
      if (avg >= 4.5) return '#FFD700'
      if (avg >= 3.5) return '#98D8C8'
      if (avg >= 2.5) return '#B0C4DE'
      if (avg >= 1.5) return '#A8A8C8'
      return '#E8A0A0'
    },
    getAvgScore(day) {
      const dateStr = `${this.year}-${this.month+1}-${day}`
      const dayEntries = this.entries.filter(e => e.date === dateStr)
      if (dayEntries.length === 0) return 0
      return dayEntries.reduce((s,e) => s + e.score, 0) / dayEntries.length
    },
    prevMonth() { this.currentDate = new Date(this.year, this.month - 1) },
    nextMonth() { this.currentDate = new Date(this.year, this.month + 1) },
    openMood(date) {
      this.selectedDate = date
      this.modalOpen = true
      this.quote = ''
    },
    saveMood(type) {
      const mood = this.moods[type]
      this.entries.push({
        date: this.selectedDate,
        mood: type,
        score: mood.score,
        emoji: mood.emoji,
        timestamp: Date.now()
      })
      this.saveEntries()
      
      const quotes = {
        awesome: ['Ты супер! Так держать!', 'Заряжайся позитивом!'],
        good: ['Хороший день — хорошее настроение!', 'Продолжай в том же духе!'],
        normal: ['Нормально — это тоже хорошо!', 'Завтра будет ещё лучше!'],
        sad: ['Всё наладится', 'Это тоже пройдёт'],
        bad: ['Дыши, всё будет хорошо', 'Давай что-то приятное?']
      }
      const q = quotes[type] || quotes.normal
      this.quote = q[Math.floor(Math.random() * q.length)]
      
      setTimeout(() => {
        this.modalOpen = false
      }, 2000)
    }
  }
}
</script>

<style scoped>
.calendar {
  max-width: 500px;
  margin: 0 auto;
}
.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.calendar-header button {
  background: #6b4c7a;
  color: white;
  border: none;
  width: 35px;
  height: 35px;
  border-radius: 50%;
  cursor: pointer;
}
.weekdays {
  display: grid;
  grid-template-columns: repeat(7,1fr);
  text-align: center;
  margin: 10px 0;
}
.calendar-days {
  display: grid;
  grid-template-columns: repeat(7,1fr);
  gap: 5px;
}
.day {
  aspect-ratio: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 10px;
  cursor: pointer;
  transition: 0.2s;
}
.day:hover {
  transform: scale(1.05);
}
.chart {
  margin-top: 30px;
}
.chart-bars {
  display: flex;
  gap: 2px;
  height: 100px;
  align-items: flex-end;
}
.chart-bar {
  flex: 1;
  background: #6b4c7a;
  min-height: 2px;
  border-radius: 3px 3px 0 0;
}
.modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0,0,0,0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}
.modal-content {
  background: white;
  padding: 30px;
  border-radius: 30px;
  text-align: center;
  max-width: 300px;
}
.emojis {
  display: flex;
  gap: 15px;
  justify-content: center;
  margin: 20px 0;
}
.emoji {
  font-size: 35px;
  cursor: pointer;
  transition: 0.2s;
}
.emoji:hover {
  transform: scale(1.2);
}
.quote {
  color: #6b4c7a;
  margin: 15px 0;
}
</style>