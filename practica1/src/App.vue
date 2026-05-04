<template>
  <div id="app">
    <header>
      <h1>🌸 Трекер настроения</h1>
      <p>Отмечай эмоции, получай советы и поднимай настроение!</p>
    </header>

    <main>
      <Calendar />
    </main>

    <FloatButton />
    <MiniGame v-if="showGame" @close="showGame = false" />

    <footer>
      <p>✨ Каждый день — новая эмоция ✨</p>
    </footer>
  </div>
</template>

<script>
import Calendar from './components/Calendar.vue'
import FloatButton from './components/FloatButton.vue'
import MiniGame from './components/MiniGame.vue'

export default {
  name: 'App',
  components: {
    Calendar,
    FloatButton,
    MiniGame
  },
  data() {
    return {
      showGame: false
    }
  },
  mounted() {
    window.addEventListener('open-game', () => {
      this.showGame = true
    })
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 0;
}

#app {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

header {
  text-align: center;
  padding: 30px 20px;
  background: rgba(255, 255, 255, 0.6);
  border-radius: 30px;
  margin-bottom: 20px;
}

header h1 {
  font-size: 2rem;
  color: #2c5f7a;
}

footer {
  text-align: center;
  padding: 20px;
  color: #aaa;
  margin-top: auto;
}
</style>
