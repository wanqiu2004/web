<template>
  <div class="container">
    <SliderCaptcha class="captcha-wrapper"/>
    <hr />
    <Test />
  </div>
</template>

<script setup>
import SliderCaptcha from './components/SliderCaptcha.vue'
import Test from './components/Test.vue';
</script>

<style>
/* 重置 body 样式，让它充满整个视口 */
body {
  margin: 0;
  padding: 0;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: flex-start; /* 垂直方向从顶部开始，避免内容垂直居中 */
  background-color:antiquewhite;
}

.container {
  width: 750px;
  display: flex;
  flex-direction: column;
  align-items: center; /* 水平居中子元素 */
}

hr {
  width: 100%;
  height: 2px;
  border-style: dashed;
  margin: 30px 0;
}

.captcha-wrapper { 
  margin-top: 50px;
}
</style>