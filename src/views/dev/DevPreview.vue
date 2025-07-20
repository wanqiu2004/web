<template>
    <div class="dev-preview-container">
        <section class="debug-section">
            <h2>Login 测试</h2>
            <Login />
        </section>

        <section class="debug-section">
            <h2>SignUp 测试</h2>
            <SignUp />
        </section>

        <section class="debug-section">
            <h2>VideoCard 测试</h2>
            <VideoCard :coverUrl="'http://localhost:9090/api/videos/index/cover'"
                :previewUrl="'http://localhost:9090/api/videos/index/preview'"
                :title="'Vue 3 从入门到精通 —— 第 ' + n + ' 篇教程'" :playCount="32145 + n" :duration="'03:21'"
                :authorName="'晚秋老师'" :authorAvatar="'http://localhost:9090/api/videos/avatar/avatar'"
                :publishTime="'2025-07-13T12:34:56Z'" :isBookmarked="false" :tags="['Vue3', '教程', '前端']" />
        </section>

        <section class="debug-section">
            <h2>ServerUptime 测试</h2>
            <ServerUptime startTime="2025-04-18T21:52:00" />
        </section>
    </div>
</template>

<script setup>
import VideoCard from '../../components/VideoCard.vue'
import ServerUptime from '../../components/ServerUptime.vue'
import Login from '../../components/Login.vue'
import SignUp from '../../components/SignUp.vue'

const n = 1
</script>

<style scoped>
.dev-preview-container {
    max-height: 100vh;
    /* 占满整个视口 */
    overflow-y: auto;
    /* 支持滚动 */
    padding: 2rem 1rem;
    box-sizing: border-box;
    background: #f9f9f9;
}

.debug-section {
    margin-bottom: 2rem;
    padding: 1rem;
    border: 1px dashed #bbb;
    background-color: #fff;
    border-radius: 8px;
}
</style>
<style>
body {
    display: flex;
    justify-content: center;
    align-items: center;
}
</style>