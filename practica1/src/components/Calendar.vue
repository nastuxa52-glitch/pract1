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
      <div v-for="n in 30" :key="n" class="bar" :style="{ height: getAvg(n) * 20 + '%' }"></div>
    </div>

    <div v-if="modal" class="modal" @click.self="modal = false">
      <div class="modal-box">
        <h3>{{ selectedDate }}</h3>
        <div class="emojis">
          <span v-for="(m, key) in moods" :key="key" @click="save(key)" class="emoji">{{ m.emoji }}</span>
        </div>
        <p v-if="quote" class="quote">{{ quote }}</p>
        <button @click="modal = false">Закрыть</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      date: new Date(),
      moods: { awesome: { emoji: '🤩', score: 5 }, good: { emoji: '😊', score: 4 }, normal: { emoji: '😐', score: 3 }, sad: { emoji: '😢', score: 2 }, bad: { emoji: '😤', score: 1 } },
      entries: [],
      modal: false,
      selectedDate: '',
      quote: ''
    }
  },
  computed: {
    year() { return this.date.getFullYear() },
    month() { return this.date.getMonth() },
    monthName() { return ['Янв','Фев','Мар','Апр','Май','Июн','Июл','Авг','Сен','Окт','Ноя','Дек'][this.month] },
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
      if (!dayEntries.length) return '#eee'
      const avg = dayEntries.reduce((s,e) => s + e.score, 0) / dayEntries.length
      if (avg >= 4) return '#FFD700'
      if (avg >= 3) return '#98D8C8'
      if (avg >= 2) return '#B0C4DE'
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
    openMood(date) { if (date) { this.selectedDate = date; this.modal = true; this.quote = '' } },
    save(type) {
      this.entries.push({ date: this.selectedDate, score: this.moods[type].score, emoji: this.moods[type].emoji, time: Date.now() })
      this.saveEntries()
      const quotes = { awesome: 'Супер! Так держать!', good: 'Отлично!', normal: 'Норм, бывает лучше', sad: 'Всё наладится', bad: 'Плохой день пройдёт' }
      this.quote = quotes[type]
      setTimeout(() => { this.quote = '' }, 1500)
    }
  }
}
</script>

<style scoped>
.calendar { max-width: 500px; margin: 0 auto; }
.header { display: flex; justify-content: space-between; align-items: center; }
.header button { background: #6b4c7a; color: white; border: none; width: 35px; height: 35px; border-radius: 50%; cursor: pointer; }
.weekdays { display: grid; grid-template-columns: repeat(7,1fr); text-align: center; margin: 10px 0; }
.days { display: grid; grid-template-columns: repeat(7,1fr); gap: 5px; }
.day { aspect-ratio: 1; display: flex; align-items: center; justify-content: center; border-radius: 10px; cursor: pointer; background: #f5f5f5; }
.chart { display: flex; gap: 2px; height: 100px; align-items: flex-end; margin-top: 20px; }
.bar { flex: 1; background: #6b4c7a; min-height: 2px; border-radius: 3px 3px 0 0; }
.modal { position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.5); display: flex; align-items: center; justify-content: center; }
.modal-box { background: white; padding: 25px; border-radius: 25px; text-align: center; min-width: 250px; }
.emojis { display: flex; gap: 15px; justify-content: center; margin: 15px 0; }
.emoji { font-size: 35px; cursor: pointer; }
.quote { color: #6b4c7a; margin: 10px 0; }
</style>