<template>
  <div class="calendar-container">
    <div class="calendar-header">
      <button @click="prevMonth" class="nav-btn">◀</button>
      <h2>{{ currentMonthName }} {{ currentYear }}</h2>
      <button @click="nextMonth" class="nav-btn">▶</button>
    </div>

    <div class="weekdays">
      <div v-for="day in weekdays" :key="day">{{ day }}</div>
    </div>

    <div class="calendar-days">
      <div 
        v-for="day in daysInMonth" 
        :key="day.date"
        class="calendar-day"
        :class="getMoodClass(day.date)"
        @click="showMoodInfo(day.date)"
      >
        <span class="day-number">{{ day.day }}</span>
        <span v-if="getMoodForDate(day.date)" class="mood-emoji">
          {{ getMoodForDate(day.date).emoji }}
        </span>
      </div>
    </div>

    <div class="legend">
      <div v-for="mood in moods" :key="mood.type" class="legend-item">
        <span class="legend-color" :style="{ background: mood.color }"></span>
        <span>{{ mood.label }} {{ mood.emoji }}</span>
      </div>
    </div>

    <AddMood 
      v-if="showAddMood" 
      :selectedDate="selectedDate"
      @close="showAddMood = false"
      @save="saveMood"
    />
  </div>
</template>

<script>
import AddMood from './AddMood.vue'

export default {
  name: 'Calendar',
  components: { AddMood },
  data() {
    return {
      currentDate: new Date(),
      weekdays: ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'],
      moods: [
        { type: 'awesome', label: 'Супер', emoji: '🤩', color: '#FFD700' },
        { type: 'good', label: 'Хорошо', emoji: '😊', color: '#98D8C8' },
        { type: 'normal', label: 'Нормально', emoji: '😐', color: '#B0C4DE' },
        { type: 'sad', label: 'Грустно', emoji: '😢', color: '#A8A8C8' },
        { type: 'bad', label: 'Плохо', emoji: '😤', color: '#E8A0A0' }
      ],
      entries: [],
      showAddMood: false,
      selectedDate: null
    }
  },
  computed: {
    currentYear() {
      return this.currentDate.getFullYear()
    },
    currentMonth() {
      return this.currentDate.getMonth()
    },
    currentMonthName() {
      const names = ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь']
      return names[this.currentMonth]
    },
    daysInMonth() {
      const year = this.currentYear
      const month = this.currentMonth
      const firstDayOfMonth = new Date(year, month, 1)
      const startDayOfWeek = firstDayOfMonth.getDay() || 7
      const daysInMonth = new Date(year, month + 1, 0).getDate()
      
      const days = []
      const prevMonthDays = startDayOfWeek - 1
      
      // Дни предыдущего месяца
      for (let i = prevMonthDays - 1; i >= 0; i--) {
        const date = new Date(year, month, -i)
        days.push({ date: this.formatDate(date), day: date.getDate(), isCurrentMonth: false })
      }
      
      // Дни текущего месяца
      for (let i = 1; i <= daysInMonth; i++) {
        const date = new Date(year, month, i)
        days.push({ date: this.formatDate(date), day: i, isCurrentMonth: true })
      }
      
      // Дни следующего месяца
      const remainingDays = 42 - days.length
      for (let i = 1; i <= remainingDays; i++) {
        const date = new Date(year, month + 1, i)
        days.push({ date: this.formatDate(date), day: i, isCurrentMonth: false })
      }
      
      return days
    }
  },
  mounted() {
    this.loadEntries()
  },
  methods: {
    formatDate(date) {
      return `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(2, '0')}-${String(date.getDate()).padStart(2, '0')}`
    },
    loadEntries() {
      const saved = localStorage.getItem('moodEntries')
      if (saved) {
        this.entries = JSON.parse(saved)
      }
    },
    saveEntries() {
      localStorage.setItem('moodEntries', JSON.stringify(this.entries))
    },
    getMoodForDate(dateStr) {
      const entry = this.entries.find(e => e.date === dateStr)
      if (entry) {
        const mood = this.moods.find(m => m.type === entry.mood)
        return { ...entry, emoji: mood?.emoji || '😐', color: mood?.color }
      }
      return null
    },
    getMoodClass(dateStr) {
      const mood = this.getMoodForDate(dateStr)
      if (mood && mood.isCurrentMonth !== false) {
        return `mood-${mood.mood}`
      }
      return ''
    },
    prevMonth() {
      this.currentDate = new Date(this.currentYear, this.currentMonth - 1)
    },
    nextMonth() {
      this.currentDate = new Date(this.currentYear, this.currentMonth + 1)
    },
    showMoodInfo(dateStr) {
      this.selectedDate = dateStr
      this.showAddMood = true
    },
    saveMood(moodData) {
      const existingIndex = this.entries.findIndex(e => e.date === moodData.date)
      if (existingIndex !== -1) {
        this.entries[existingIndex] = moodData
      } else {
        this.entries.push(moodData)
      }
      this.saveEntries()
      this.showAddMood = false
    }
  }
}
</script>

<style scoped>
.calendar-container {
  background: white;
  border-radius: 30px;
  padding: 20px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.1);
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.nav-btn {
  background: #6b4c7a;
  color: white;
  border: none;
  width: 35px;
  height: 35px;
  border-radius: 50%;
  cursor: pointer;
  font-size: 18px;
}

.weekdays {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  text-align: center;
  font-weight: bold;
  color: #6b4c7a;
  margin-bottom: 10px;
}

.calendar-days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 5px;
}

.calendar-day {
  aspect-ratio: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-radius: 12px;
  cursor: pointer;
  transition: transform 0.2s;
  position: relative;
  background: #f9f9f9;
}

.calendar-day:hover {
  transform: scale(1.05);
}

.day-number {
  font-size: 14px;
  font-weight: bold;
}

.mood-emoji {
  font-size: 20px;
}

/* Цвета настроений */
.mood-awesome { background: #FFD700; }
.mood-good { background: #98D8C8; }
.mood-normal { background: #B0C4DE; }
.mood-sad { background: #A8A8C8; }
.mood-bad { background: #E8A0A0; }

.legend {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 15px;
  margin-top: 20px;
  padding-top: 15px;
  border-top: 1px solid #eee;
}

.legend-item {
  display: flex;
  align-items: center;
  gap: 5px;
  font-size: 12px;
}

.legend-color {
  width: 15px;
  height: 15px;
  border-radius: 50%;
}
</style>