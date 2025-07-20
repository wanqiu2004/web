<!-- ServerUptime.vue -->
<template>
  <div>
    服务器已运行：{{ uptime }}
  </div>
</template>

<script setup>
import { computed, ref, onMounted, onBeforeUnmount } from 'vue'

// 父组件传递的启动时间字符串
const props = defineProps({
  startTime: {
    type: String,
    required: true
  }
})

const elapsed = ref(0)
let timerId = null

onMounted(() => {
  const startTimestamp = new Date(props.startTime).getTime()
  updateElapsed(startTimestamp)

  timerId = setInterval(() => {
    updateElapsed(startTimestamp)
  }, 1000)
})

onBeforeUnmount(() => {
  if (timerId) clearInterval(timerId)
})

function updateElapsed(start) {
  const now = Date.now()
  elapsed.value = Math.floor((now - start) / 1000) // 秒
}

const uptime = computed(() => {
  const seconds = elapsed.value % 60
  const minutes = Math.floor(elapsed.value / 60) % 60
  const hours = Math.floor(elapsed.value / 3600) % 24
  const days = Math.floor(elapsed.value / 86400)

  return `${days}天 ${pad(hours)}小时 ${pad(minutes)}分 ${pad(seconds)}秒`
})

function pad(num) {
  return String(num).padStart(2, '0')
}
</script>
