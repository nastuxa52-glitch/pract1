<template>
  <button class="float-btn" @click="openGame">
    🧁 Поднять настроение
  </button>
</template>

<script>
export default {
  name: 'FloatButton',
  methods: {
    openGame() {
      window.dispatchEvent(new CustomEvent('open-game'))
    }
  }
}
</script>

<style scoped>
.float-btn {
  position: fixed;
  bottom: 30px;
  right: 30px;
  background: linear-gradient(135deg, #ff9a9e 0%, #fad0c4 100%);
  border: none;
  color: #5a3a4a;
  padding: 12px 24px;
  border-radius: 50px;
  font-weight: bold;
  cursor: pointer;
  box-shadow: 0 5px 15px rgba(0,0,0,0.2);
  transition: transform 0.2s;
  z-index: 100;
}

.float-btn:hover {
  transform: scale(1.05);
}
</style>