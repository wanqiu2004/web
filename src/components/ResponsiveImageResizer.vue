<template>
  <div class="container">
    <p class="heading">HFkKDIQll2RwqJayeogCQjMQVg</p>

    <div class="image-container">
      <TransitionGroup name="zoom" tag="div" class="image-container">
        <div
          key="img1"
          class="thumbnail"
          :style="thumbnailStyle('/img/in.jpg')"
        ></div>
        <div
          key="img2"
          class="thumbnail"
          :style="thumbnailStyle('/img/preview.png')"
        ></div>
      </TransitionGroup>
    </div>

    <div class="input-group">
      <input
        type="text"
        class="dimension-input"
        v-model.lazy="height"
        placeholder="高"
        @blur="handleBlur"
      />
      <input
        type="text"
        class="dimension-input"
        v-model.lazy="width"
        placeholder="宽"
        @blur="handleBlur"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'

const width = ref(160)
const height = ref(90)
const lastConfettiTime = ref(0)

// 从 localStorage 初始化
onMounted(() => {
  const savedWidth = parseInt(localStorage.getItem('image-width'))
  const savedHeight = parseInt(localStorage.getItem('image-height'))
  if (!isNaN(savedWidth)) width.value = savedWidth
  if (!isNaN(savedHeight)) height.value = savedHeight
})

// 保存到 localStorage
watch([width, height], ([w, h]) => {
  localStorage.setItem('image-width', String(w))
  localStorage.setItem('image-height', String(h))
})

// 生成图片样式
function thumbnailStyle(url) {
  return {
    backgroundImage: `url(${url})`,
    width: `${width.value}px`,
    height: `${height.value}px`,
  }
}

// 节流 + 粒子动画
function handleBlur() {
  const now = Date.now()
  if (now - lastConfettiTime.value > 1000) {
    lastConfettiTime.value = now
    launchConfettiBurst()
  }
}

// 多点位持续性爆破 🎉
function launchConfettiBurst() {
  if (!window.confetti) return
  const count = 5
  const interval = 150
  let i = 0

  const timer = setInterval(() => {
    if (i++ >= count) {
      clearInterval(timer)
      return
    }

    window.confetti({
      particleCount: 80,
      spread: 70 + Math.random() * 30,
      origin: {
        x: Math.random(),
        y: Math.random() * 0.6 + 0.2,
      },
      scalar: 0.8 + Math.random() * 0.6,
      colors: ['#e91e63', '#2196f3', '#4caf50', '#ff9800', '#9c27b0'],
    })
  }, interval)
}
</script>

<style scoped>
@font-face {
  font-family: "AlfaSlabOne";
  src: url("/font/AlfaSlabOne-Regular.ttf") format("truetype");
}

.container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: white;
  font-family: Arial, sans-serif;
}

.heading {
  font-size: 18px;
  font-family: "AlfaSlabOne", sans-serif;
  margin-bottom: 20px;
  background-color: antiquewhite;
  padding: 5px 10px;
  border-radius: 6px;
  user-select: all;
}

.image-container {
  display: flex;
  gap: 40px;
  margin-bottom: 20px;
}

.thumbnail {
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  border: 2px dashed #ddd;
  border-radius: 6px;
  transition: all 0.4s ease;
}

.thumbnail:hover {
  border-color: #FF69B4;
}

.input-group {
  display: flex;
  gap: 20px;
}

.dimension-input {
  padding: 8px;
  width: 88px;
  border: 2px dashed black;
  border-radius: 6px;
  outline: none;
  font-size: 16px;
  text-align: center;
  transition: border-color 0.3s ease, border-style 0.3s ease;
}

.dimension-input:focus {
  border-color: black;
  border-style: solid;
}

/* 过渡动画 */
.zoom-enter-active,
.zoom-leave-active {
  transition: transform 0.4s ease;
}
.zoom-enter-from,
.zoom-leave-to {
  transform: scale(0.95);
}
</style>
