<template>
  <div class="video-card" @mouseenter="handleMouseEnter" @mouseleave="handleMouseLeave">
    <!-- 视频展示区 -->
    <div class="video-media" @click="goToVideo">
      <img :src="coverUrl" class="video-cover" ref="coverRef" />
      <video :src="previewUrl" class="video-preview" ref="videoRef" preload="auto" loop muted />
    </div>

    <!-- 视频信息区 -->
    <div class="video-meta">
      <div class="title-row">
        <div class="title" @click="goToVideo">{{ title }}</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'

const props = defineProps({
  videoId: String,
  coverUrl: String,
  previewUrl: String,
  title: String,
  playCount: Number,
  duration: String,
  authorName: String,
  authorAvatar: String,
  publishTime: String,
  isBookmarked: Boolean,
  tags: {
    type: Array,
    default: () => []
  }
})

const videoRef = ref(null)
const coverRef = ref(null)
const bookmarked = ref(props.isBookmarked)
const router = useRouter()

function handleMouseEnter() {
  if (coverRef.value) coverRef.value.style.opacity = '0'
  if (videoRef.value) {
    videoRef.value.style.opacity = '1'
    videoRef.value.play()
  }
}

function handleMouseLeave() {
  if (videoRef.value) {
    videoRef.value.pause()
    videoRef.value.currentTime = 0
    videoRef.value.style.opacity = '0'
  }
  if (coverRef.value) coverRef.value.style.opacity = '1'
}

function toggleBookmark() {
  bookmarked.value = !bookmarked.value
  // TODO: 发送到后端
}

function goToVideo() {
  router.push(`/watch/${props.videoId}`)
}

const relativeTime = computed(() => {
  const diff = Date.now() - new Date(props.publishTime).getTime()
  const minutes = Math.floor(diff / 60000)
  const hours = Math.floor(minutes / 60)
  const days = Math.floor(hours / 24)
  if (days >= 1) return `${days} 天前`
  if (hours >= 1) return `${hours} 小时前`
  return `${minutes} 分钟前`
})
</script>

<style scoped>
.author-info {
  display: flex;
  align-items: center;
}

.bookmark-btn {
  margin-left: auto;
}

.video-card {
  width: 320px;
  border-radius: 4px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
  background-color: white;
}

.video-media {
  position: relative;
  width: 100%;
  height: 180px;
  overflow: hidden;
  background-color: black;
}

.video-cover,
.video-preview {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: opacity 0.5s ease;
  position: absolute;
  top: 0;
  left: 0;
}

.video-preview {
  opacity: 0;
  object-fit: contain;
}

.video-meta {
  padding: 12px;
}

.title-row {
  display: flex;
  user-select: none;
  justify-content: space-between;
  align-items: center;
  font-weight: bold;
  font-size: 14px;
  margin-bottom: 4px;
}

.bookmark-btn {
  background: none;
  border: none;
  cursor: pointer;
  padding: 0;
}

.icon {
  width: 20px;
  height: 20px;
  fill: #999;
}
.bookmarked {
  fill: gold;
}

.author-info {
  display: flex;
  align-items: center;
  font-size: 12px;
  color: #666;
  margin-bottom: 4px;
}

.avatar {
  width: 22px;
  height: 22px;
  border-radius: 50%;
  margin-right: 6px;
}

.tag-row {
  margin-top: 4px;
}



.title {
  font-size: 12px;
  font-weight: 600;
  color: #222;
  cursor: pointer;
  line-height: 1.3;
  position: relative; /* ✅ 必须有 */
  transition: color 0.2s ease-in-out;
}

.title::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: -3px;
  height: 2px;
  width: 100%;
  background-color: #ec4556;
  transform: scaleX(0);
  transform-origin: left center;
  border-radius: 1px;
  transition: transform 0.3s ease;
  pointer-events: none;
}

.title:hover {
  color: #ec4556;
}

.title:hover::after {
  transform: scaleX(1);
}

.author-info {
  display: flex;
  align-items: center;
  font-size: 13px;
  color: #666;
  gap: 8px;
  margin-bottom: 10px;
}

.author-info .avatar {
  width: 28px;
  height: 28px;
  border-radius: 50%;
  object-fit: cover;
  flex-shrink: 0;
  border: 1.5px solid #ddd;
}

.author-info .author-name {
  font-weight: 500;
  color: #444;
}

.author-info .time,
.author-info .duration {
  color: #999;
}

.bookmark-btn {
  margin-left: auto;
  background: transparent;
  border: none;
  cursor: pointer;
  padding: 4px;
  display: flex;
  align-items: center;
  color: #999;
  transition: color 0.2s ease-in-out;
}

.bookmark-btn:hover {
  color: #000; /* 橙红色，突出收藏 */
}

.bookmark-btn .icon {
  width: 20px;
  height: 20px;
  fill: currentColor;
}

.bookmark-btn .bookmarked {
  color: #fa541c;
}

.tag-row {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
}

/* a-tag的简单样式，假设你没用UI库 */
a-tag {
  display: inline-block;
  background-color: #e6f7ff;
  color: #1890ff;
  font-size: 12px;
  padding: 2px 8px;
  border-radius: 12px;
  user-select: none;
  cursor: default;
  transition: background-color 0.3s ease;
}

a-tag:hover {
  background-color: #bae7ff;
}

</style>
