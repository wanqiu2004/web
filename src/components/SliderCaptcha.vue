<template>
  <div class="slider-captcha">
    <div class="captcha-container" ref="containerRef">
      <!-- 背景图画布 -->
      <canvas ref="canvasBg" class="captcha-bg"></canvas>

      <!-- 滑块拼图 canvas 嵌套在 block-wrapper 中一起拖动 -->
      <div class="block-wrapper" :style="{ left: `${sliderLeft}px` }">
        <canvas ref="canvasBlock" class="captcha-block"></canvas>
      </div>

      <!-- 滑动条 -->
      <div class="slider-wrapper">
        <div class="slider-track" :class="{ success: verified }"></div>
        <div
          class="slider-btn"
          :style="{ left: `${sliderLeft}px` }"
          @mousedown="startDrag"
          @touchstart="startDrag"
        >
          <span class="icon">⇌</span>
        </div>
      </div>
    </div>

    <div class="result-text" :class="{ success: verified, fail: verificationFailed }">
      {{ resultMessage }}
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const containerRef = ref()
const canvasBg = ref()
const canvasBlock = ref()

const sliderLeft = ref(0)
const startX = ref(0)
const isDragging = ref(false)

const blockX = ref(0) // 缺口横坐标
const blockY = ref(0) // 缺口纵坐标
const verified = ref(false)
const verificationFailed = ref(false)

const resultMessage = ref('请拖动滑块完成验证')

const imgSrc = '/img/slider.jpg'
const canvasWidth = 310
const canvasHeight = 155
const blockSize = 42

const drawCaptcha = () => {
  const bgCtx = canvasBg.value.getContext('2d')
  const blockCtx = canvasBlock.value.getContext('2d')

  const img = new Image()
  img.crossOrigin = 'Anonymous'
  img.onload = () => {
    blockY.value = 20 + Math.random() * (canvasHeight - blockSize - 20)
    blockX.value = 60 + Math.random() * (canvasWidth - blockSize - 60)

    // 画背景图
    canvasBg.value.width = canvasWidth
    canvasBg.value.height = canvasHeight
    bgCtx.clearRect(0, 0, canvasWidth, canvasHeight)
    bgCtx.drawImage(img, 0, 0, canvasWidth, canvasHeight)

    // 扣除缺口
    bgCtx.save()
    drawPuzzleShape(bgCtx, blockX.value, blockY.value)
    bgCtx.globalCompositeOperation = 'destination-out'
    bgCtx.fill()
    bgCtx.restore()

    // 绘制滑动块图像
    canvasBlock.value.width = blockSize
    canvasBlock.value.height = canvasHeight
    blockCtx.clearRect(0, 0, blockSize, canvasHeight)
    blockCtx.save()
    drawPuzzleShape(blockCtx, 0, blockY.value)
    blockCtx.clip()
    blockCtx.drawImage(
      img,
      blockX.value,
      0,
      blockSize,
      canvasHeight,
      0,
      0,
      blockSize,
      canvasHeight
    )
    blockCtx.restore()

    sliderLeft.value = 0
    resultMessage.value = '请拖动滑块完成验证'
  }
  img.src = imgSrc
}

const drawPuzzleShape = (ctx, x, y) => {
  const r = 8
  ctx.beginPath()
  ctx.moveTo(x, y)
  ctx.lineTo(x + blockSize / 2 - r, y)
  ctx.arc(x + blockSize / 2, y, r, Math.PI, 0, true)
  ctx.lineTo(x + blockSize, y)
  ctx.lineTo(x + blockSize, y + blockSize)
  ctx.lineTo(x, y + blockSize)
  ctx.lineTo(x, y)
  ctx.closePath()
}

const startDrag = (e) => {
  if (verified.value) return
  isDragging.value = true
  startX.value = e.clientX || e.touches[0].clientX
  document.addEventListener('mousemove', onDrag)
  document.addEventListener('mouseup', endDrag)
  document.addEventListener('touchmove', onDrag)
  document.addEventListener('touchend', endDrag)
}

const onDrag = (e) => {
  if (!isDragging.value) return
  const clientX = e.clientX || e.touches[0].clientX
  const deltaX = clientX - startX.value
  if (deltaX >= 0 && deltaX <= canvasWidth - blockSize) {
    sliderLeft.value = deltaX
  }
}

const endDrag = () => {
  if (!isDragging.value) return
  isDragging.value = false
  document.removeEventListener('mousemove', onDrag)
  document.removeEventListener('mouseup', endDrag)
  document.removeEventListener('touchmove', onDrag)
  document.removeEventListener('touchend', endDrag)

  const tolerance = 6
  if (Math.abs(sliderLeft.value - blockX.value) < tolerance) {
    verified.value = true
    resultMessage.value = '验证成功 🎉'
  } else {
    verificationFailed.value = true
    resultMessage.value = '验证失败，请重试'
    setTimeout(() => {
      verificationFailed.value = false
      drawCaptcha()
    }, 1500)
  }
}

onMounted(() => {
  drawCaptcha()
})
</script>

<style scoped>
.slider-captcha {
  width: 320px;
  margin: auto;
  font-family: 'Segoe UI', sans-serif;
}

.captcha-container {
  position: relative;
  width: 310px;
  height: 155px;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 0 6px rgba(0, 0, 0, 0.2);
}

canvas {
  display: block;
}

.captcha-bg {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 1;
}

.block-wrapper {
  position: absolute;
  top: 0;
  width: 42px;
  height: 100%;
  z-index: 3;
  pointer-events: none;
  transition: left 0.1s ease;
}

.captcha-block {
  width: 100%;
  height: 100%;
  display: block;
}

.slider-wrapper {
  position: relative;
  margin-top: 165px;
  height: 40px;
  background: #f0f0f0;
  border-radius: 6px;
  box-shadow: inset 0 0 4px rgba(0, 0, 0, 0.1);
}

.slider-track {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  background: #69c0ff;
  width: 100%;
  border-radius: 6px;
  opacity: 0;
  transition: opacity 0.3s;
}

.slider-track.success {
  opacity: 1;
}

.slider-btn {
  position: absolute;
  top: 0;
  width: 40px;
  height: 100%;
  background: #ffffff;
  border: 1px solid #ccc;
  border-radius: 6px;
  cursor: grab;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 4;
  transition: background 0.3s;
}

.slider-btn:hover {
  background: #e6f7ff;
}

.icon {
  font-size: 18px;
  user-select: none;
}

.result-text {
  margin-top: 10px;
  text-align: center;
  font-size: 14px;
  transition: color 0.3s;
}

.result-text.success {
  color: #52c41a;
}

.result-text.fail {
  color: #ff4d4f;
}
</style>
