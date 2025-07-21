<template>
    <div class="video-container" ref="container" @mousemove="handleMouseMove" @mouseleave="handleMouseLeave">
        <video ref="video" class="video" playsinline preload="metadata" src="http://localhost:8000/index.mp4"
            @loadedmetadata="onVideoLoaded"></video>

        <!-- 控制栏 -->
        <transition name="fade">
            <div v-show="controlsVisible" class="controls" @mouseenter="cancelHideTimeout"
                @mouseleave="startHideTimeout">
                <!-- 播放 / 暂停 -->
                <button @click="togglePlay" class="control-btn" aria-label="Toggle Play/Pause">
                    <svg v-if="!playing" viewBox="0 0 24 24" class="icon">
                        <path d="M8 5v14l11-7z" />
                    </svg>
                    <svg v-else viewBox="0 0 24 24" class="icon">
                        <path d="M6 19h4V5H6zm8-14v14h4V5z" />
                    </svg>
                </button>

                <!-- 时间显示 -->
                <span class="time-display">{{ formatTime(currentTime) }} / {{ formatTime(duration) }}</span>

                <!-- 进度条 -->
                <!-- 进度条 -->
                <div class="progress-container" ref="progressContainer" @mousemove="updateThumbnail"
                    @mouseleave="hideThumbnail" @mousedown.prevent="startSeeking" @mouseup="stopSeeking">
                    <div class="progress-bar" ref="progressBar">
                        <div class="buffered" :style="{ width: bufferedWidth }"></div>
                        <div class="progress" :style="{ width: progressWidth }"></div>
                    </div>

                    <!-- 缩略图预览 -->
                    <div class="thumbnail-preview" v-show="showThumbnail" :style="thumbnailStyle"></div>

                    <!-- 工具提示 -->
                    <div class="tooltip" v-show="showThumbnail" :style="tooltipStyle">
                        帧：{{ thumbnailFrame + 1 }}
                    </div>
                </div>


                <!-- 静音开关 -->
                <button @click="toggleMute" class="control-btn" aria-label="Toggle Mute/Unmute">
                    <svg v-if="!muted" viewBox="0 0 24 24" class="icon">
                        <path d="M5 9v6h4l5 5V4l-5 5H5z" />
                    </svg>
                    <svg v-else viewBox="0 0 24 24" class="icon">
                        <path d="M16 12l4-4m0 8l-4-4m-6-4v12l-5-5H5V9h4l5-5z" />
                    </svg>
                </button>

                <!-- 全屏切换 -->
                <button @click="toggleFullscreen" class="control-btn" aria-label="Toggle Fullscreen">
                    <svg viewBox="0 0 24 24" class="icon">
                        <path d="M7 14H5v5h5v-2H7v-3zm0-4h2V7h3V5H7v5zm10 0V7h-3V5h5v5h-2zm0 4h2v5h-5v-2h3v-3z" />
                    </svg>
                </button>
            </div>
        </transition>
    </div>
</template>

<script setup>
// import { ref, onMounted, onBeforeUnmount } from 'vue'
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'
const video = ref(null)
const container = ref(null)
const progressBar = ref(null)

const playing = ref(false)
const currentTime = ref(0)
const duration = ref(0)
const muted = ref(false)
const controlsVisible = ref(true)
let hideTimeout = null
let seeking = false

function formatTime(seconds) {
    const minutes = Math.floor(seconds / 60)
    const secs = Math.floor(seconds % 60)
    return `${String(minutes).padStart(2, '0')}:${String(secs).padStart(2, '0')}`
}

function togglePlay() {
    if (!video.value) return
    video.value.paused ? video.value.play() : video.value.pause()
}

function toggleMute() {
    if (!video.value) return
    video.value.muted = !video.value.muted
    muted.value = video.value.muted
}

function toggleFullscreen() {
    if (!document.fullscreenElement) {
        container.value?.requestFullscreen().catch(err => console.error('Failed to enter fullscreen mode:', err))
    } else {
        document.exitFullscreen().catch(err => console.error('Failed to exit fullscreen mode:', err))
    }
}

function seek(event) {
    if (!video.value || !duration.value) return
    const rect = progressBar.value.getBoundingClientRect()
    const percent = (event.clientX - rect.left) / rect.width
    video.value.currentTime = percent * duration.value
}

function startSeeking(event) {
    seeking = true
    seek(event)
    document.addEventListener('mousemove', handleMouseMoveDuringSeek)
    document.addEventListener('mouseup', stopSeeking)
}

function stopSeeking() {
    seeking = false
    document.removeEventListener('mousemove', handleMouseMoveDuringSeek)
    document.removeEventListener('mouseup', stopSeeking)
}

function handleMouseMoveDuringSeek(event) {
    if (seeking) seek(event)
}

function handleMouseMove() {
    controlsVisible.value = true
    cancelHideTimeout()
    startHideTimeout()
}

function handleMouseLeave() {
    startHideTimeout()
}

function startHideTimeout() {
    if (hideTimeout) clearTimeout(hideTimeout)
    hideTimeout = setTimeout(() => {
        controlsVisible.value = false
    }, 2500)
}

function cancelHideTimeout() {
    if (hideTimeout) clearTimeout(hideTimeout)
}

function onVideoLoaded() {
    duration.value = video.value.duration
}

onMounted(() => {
    const v = video.value
    v.addEventListener('timeupdate', () => {
        currentTime.value = v.currentTime
    })
    v.addEventListener('play', () => (playing.value = true))
    v.addEventListener('pause', () => (playing.value = false))
    v.addEventListener('volumechange', () => (muted.value = v.muted))

    document.addEventListener('fullscreenchange', () => {
        if (!document.fullscreenElement) controlsVisible.value = true
    })
})

onBeforeUnmount(() => {
    video.value?.pause()
    cancelHideTimeout()
})












// 缩略图逻辑相关
const showThumbnail = ref(false)
const thumbnailFrame = ref(0)
const thumbnailStyle = ref({})
const tooltipStyle = ref({})

// 图片拼图参数（根据实际配置）
const frameWidth = 10
const frameHeight = 90
const totalFrames = 2320
const thumbnailWidth = 160
const imageWidth = 1600
const imageHeight = 20880

const progressWidth = computed(() =>
  duration.value ? `${(currentTime.value / duration.value) * 100}%` : '0%'
)

const bufferedWidth = computed(() => {
  const buffered = video.value?.buffered
  if (!buffered || buffered.length === 0) return '0%'
  const end = buffered.end(buffered.length - 1)
  return `${(end / duration.value) * 100}%`
})

// 更新缩略图
function updateThumbnail(e) {
  if (!progressContainer.value || !progressBar.value || !duration.value) return
  const rect = progressContainer.value.getBoundingClientRect()
  const barWidth = rect.width
  const mouseX = e.clientX - rect.left

  const frameIndex = Math.floor(mouseX / (barWidth / totalFrames))
  thumbnailFrame.value = frameIndex

  const row = Math.floor(frameIndex / frameWidth)
  const col = frameIndex % frameWidth
  const backgroundPositionX = -(col * thumbnailWidth)
  const backgroundPositionY = -(row * frameHeight)

  thumbnailStyle.value = {
    backgroundImage: "url('http://localhost:8000/preview.jpg')",
    backgroundPosition: `${backgroundPositionX}px ${backgroundPositionY}px`,
    left: `${mouseX - thumbnailWidth / 2}px`,
    top: `-110px`
  }

  tooltipStyle.value = {
    left: `${mouseX - 20}px`,
    top: `-25px`
  }

  showThumbnail.value = true
}

function hideThumbnail() {
  showThumbnail.value = false
}
</script>

<style scoped>
.video-container {
    position: relative;
    background: #000;
    max-width: 960px;
    margin: auto;
    user-select: none;
    cursor: default;
    overflow: hidden;
}

.video {
    width: 100%;
    height: auto;
    display: block;
    background-color: #000;
    cursor: pointer;
}

/* 控制栏整体 */
.controls {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    padding: 12px 24px;
    background: linear-gradient(to top, rgba(0, 0, 0, 0.6), transparent);
    display: flex;
    align-items: center;
    justify-content: space-between;
    opacity: 1;
    transition: opacity 0.3s ease;
    pointer-events: auto;
}

.controls.hidden {
    opacity: 0;
    pointer-events: none;
}

/* 按钮统一样式 */
.control-btn {
    background: transparent;
    border: none;
    padding: 8px;
    cursor: pointer;
    display: flex;
    align-items: center;
    transition: transform 0.2s ease;
}

.control-btn:hover {
    transform: scale(1.2);
}

.icon {
    width: 28px;
    height: 28px;
    fill: #fff;
}

/* 时间 */
.time-display {
    font-size: 14px;
    color: #fff;
    white-space: nowrap;
    margin: 0 16px;
}

/* 进度条 */
.progress-container {
    flex: 1;
    position: relative;
    margin: 0 16px;
    cursor: pointer;
}

.progress-bar {
    width: 100%;
    height: 6px;
    background-color: #666;
    border-radius: 3px;
    overflow: hidden;
    position: relative;
}

.progress {
    height: 100%;
    background-color: #ff3b3b;
    /* 可替换为亮蓝、亮绿等主色 */
    width: 0%;
    transition: width 0.1s linear;
}

/* 缓冲进度（可选） */
.buffered {
    height: 100%;
    background-color: #aaa;
    width: 0%;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 0;
    opacity: 0.5;
}

.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}










/* 缩略图预览框 */
.thumbnail-preview {
  position: absolute;
  width: 160px;
  height: 90px;
  background-size: 1600px 20880px;
  background-repeat: no-repeat;
  border-radius: 4px;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.6);
  pointer-events: none;
  z-index: 10;
}

/* 工具提示 */
.tooltip {
  position: absolute;
  color: #fff;
  background: rgba(0, 0, 0, 0.75);
  padding: 2px 6px;
  font-size: 12px;
  border-radius: 4px;
  white-space: nowrap;
  pointer-events: none;
  z-index: 11;
}

</style>
