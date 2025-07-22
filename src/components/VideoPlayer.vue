<template>
  <div class="video-wrapper">
    <video
      ref="videoRef"
      :src="src"
      @timeupdate="emitTimeUpdate"
      @loadedmetadata="emitDuration"
      @ended="emitEnded"
      controls
      preload="metadata"
    ></video>
  </div>
</template>

<script setup lang="ts">
import { ref, defineExpose, watch } from 'vue'

interface Props {
  src: string
}
const props = defineProps<Props>()

const emit = defineEmits<{
  (e: 'timeupdate', current: number): void
  (e: 'duration', duration: number): void
  (e: 'ended'): void
}>()

const videoRef = ref<HTMLVideoElement | null>(null)

// 暴露方法给父组件调用
function play() {
  videoRef.value?.play()
}
function pause() {
  videoRef.value?.pause()
}
function seekTo(seconds: number) {
  if (videoRef.value) {
    videoRef.value.currentTime = seconds
  }
}
function setVolume(volume: number) {
  if (videoRef.value) {
    videoRef.value.volume = volume
  }
}
function setPlaybackRate(rate: number) {
  if (videoRef.value) {
    videoRef.value.playbackRate = rate
  }
}
function setLoop(loop: boolean) {
  if (videoRef.value) {
    videoRef.value.loop = loop
  }
}

// 对外暴露接口
defineExpose({
  play,
  pause,
  seekTo,
  setVolume,
  setPlaybackRate,
  setLoop,
})

// 事件触发
function emitTimeUpdate() {
  if (videoRef.value) {
    emit('timeupdate', videoRef.value.currentTime)
  }
}
function emitDuration() {
  if (videoRef.value) {
    emit('duration', videoRef.value.duration)
  }
}
function emitEnded() {
  emit('ended')
}
</script>

<style scoped>
.video-wrapper {
  width: 100%;
  max-width: 800px;
}
video {
  width: 100%;
  height: auto;
  display: block;
}
</style>
