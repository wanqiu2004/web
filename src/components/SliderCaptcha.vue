<template>
  <div class="slider-captcha">
    <div class="captcha-container" ref="containerRef">
      <!-- 背景图 -->
      <canvas ref="canvasBg" class="captcha-bg" />

      <!-- 拼图块 -->
      <div class="block-wrapper" :style="{ left: `${sliderX}px` }">
        <canvas ref="canvasBlock" class="captcha-block" />
      </div>
    </div>

    <!-- 滑动条 -->
    <div class="slider-wrapper">
      <div class="slider-track" :class="{ success: isVerified }" />
      <div
        class="slider-btn"
        :style="{ left: `${sliderX}px` }"
        @mousedown="onStart"
        @touchstart="onStart"
      >
        <span class="icon">⇌</span>
      </div>
    </div>

    <!-- 验证结果 -->
    <div class="result-text" :class="{ success: isVerified, fail: isFailed }">
      {{ resultMessage }}
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

// DOM 引用
const containerRef = ref()
const canvasBg = ref()
const canvasBlock = ref()

// 验证逻辑状态
const sliderX = ref(0)
const startX = ref(0)
const isDragging = ref(false)

const blockX = ref(0)
const blockY = ref(0)
const isVerified = ref(false)
const isFailed = ref(false)

const resultMessage = ref('请拖动滑块完成验证')

// 常量配置
const imgSrc = '/img/slider.jpg'
const canvasWidth = 310
const canvasHeight = 155
const blockSize = 42

// 初始化
onMounted(() => {
  drawCaptcha()
})

// 生成拼图验证码
function drawCaptcha() {
  const bgCtx = canvasBg.value.getContext('2d')
  const blockCtx = canvasBlock.value.getContext('2d')

  const img = new Image()
  img.crossOrigin = 'Anonymous'
  img.onload = () => {
    // 随机缺口位置
    blockX.value = 60 + Math.random() * (canvasWidth - blockSize - 60)
    blockY.value = 20 + Math.random() * (canvasHeight - blockSize - 20)

    // 背景绘制
    canvasBg.value.width = canvasWidth
    canvasBg.value.height = canvasHeight
    bgCtx.clearRect(0, 0, canvasWidth, canvasHeight)
    bgCtx.drawImage(img, 0, 0, canvasWidth, canvasHeight)

    // 绘制缺口
    bgCtx.save()
    drawPuzzleShape(bgCtx, blockX.value, blockY.value)
    bgCtx.globalCompositeOperation = 'destination-out'
    bgCtx.fill()
    bgCtx.restore()

    // 绘制拼图块
    canvasBlock.value.width = blockSize
    canvasBlock.value.height = blockSize
    blockCtx.clearRect(0, 0, blockSize, blockSize)
    blockCtx.save()
    drawPuzzleShape(blockCtx, 0, 0)
    blockCtx.clip()
    blockCtx.drawImage(
      img,
      blockX.value,
      blockY.value,
      blockSize,
      blockSize,
      0,
      0,
      blockSize,
      blockSize
    )
    blockCtx.restore()

    sliderX.value = 0
    isVerified.value = false
    isFailed.value = false
    resultMessage.value = '请拖动滑块完成验证'
  }

  img.src = imgSrc
}

// 绘制拼图形状
function drawPuzzleShape(ctx, x, y) {
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
  ctx.fillStyle = 'rgba(255, 255, 255, 0.6)'
  ctx.fill()
}

// 拖动开始
function onStart(e) {
  if (isVerified.value) return
  isDragging.value = true
  startX.value = e.clientX || e.touches[0].clientX
  document.addEventListener('mousemove', onMove)
  document.addEventListener('mouseup', onEnd)
  document.addEventListener('touchmove', onMove)
  document.addEventListener('touchend', onEnd)
}

// 拖动中
function onMove(e) {
  if (!isDragging.value) return
  const clientX = e.clientX || e.touches[0].clientX
  const deltaX = clientX - startX.value
  const maxX = canvasWidth - blockSize
  if (deltaX >= 0 && deltaX <= maxX) {
    sliderX.value = deltaX
  }
}

// 拖动结束
function onEnd() {
  if (!isDragging.value) return
  isDragging.value = false

  document.removeEventListener('mousemove', onMove)
  document.removeEventListener('mouseup', onEnd)
  document.removeEventListener('touchmove', onMove)
  document.removeEventListener('touchend', onEnd)

  const tolerance = 6
  if (Math.abs(sliderX.value - blockX.value) < tolerance) {
    isVerified.value = true
    resultMessage.value = '验证成功 🎉'
  } else {
    isFailed.value = true
    resultMessage.value = '验证失败，请重试'
    setTimeout(() => {
      drawCaptcha()
    }, 1500)
  }
}
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
  background: #f0f0f0;
}

.captcha-bg {
  position: absolute;
  top: 0;
  left: 0;
  width: 310px;
  height: 155px;
  z-index: 1;
}

.block-wrapper {
  position: absolute;
  top: 0;
  width: 42px;
  height: 42px;
  z-index: 3;
  pointer-events: none;
  transition: left 0.1s ease;
}

.captcha-block {
  width: 100%;
  height: 100%;
  display: block;
}

/* 滑动条 */
.slider-wrapper {
  position: relative;
  margin-top: 16px;
  height: 44px;
  background: #e8e8e8;
  border-radius: 6px;
  box-shadow: inset 0 0 4px rgba(0, 0, 0, 0.1);
  overflow: hidden;
}

.slider-track {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  background: #69c0ff;
  width: 100%;
  opacity: 0;
  transition: opacity 0.3s;
  z-index: 1;
}

.slider-track.success {
  opacity: 1;
}

.slider-btn {
  position: absolute;
  top: 0;
  width: 44px;
  height: 44px;
  background: #ffffff;
  border: 2px solid #1890ff;
  border-radius: 6px;
  cursor: grab;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  transition: background 0.3s;
}

.slider-btn:hover {
  background: #e6f7ff;
}

.icon {
  font-size: 18px;
  user-select: none;
  color: #1890ff;
}

.result-text {
  margin-top: 12px;
  text-align: center;
  font-size: 14px;
  color: #888;
  transition: color 0.3s;
}

.result-text.success {
  color: #52c41a;
}

.result-text.fail {
  color: #ff4d4f;
}
</style>
