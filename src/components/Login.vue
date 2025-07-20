<template>
  <div class="login-container">
    <div class="login-box">
      <h2 class="login-title">欢迎登录</h2>
      <form class="login-form" @submit.prevent="handleLogin">
        <div class="input-group">
          <label for="username">账号</label>
          <div class="input-wrapper">
            <i class="icon user-icon"></i>
            <input
              id="username"
              v-model="username"
              type="text"
              placeholder="用户邮箱"
            />
          </div>
        </div>
        <div class="input-group">
          <label for="password">密码</label>
          <div class="input-wrapper">
            <i class="icon lock-icon"></i>
            <input
              id="password"
              v-model="password"
              type="password"
              placeholder="输入密码"
            />
          </div>
        </div>
        <div class="input-group">
          <label for="captcha">验证码</label>
          <div class="captcha-wrapper">
            <div class="captcha-container">
              <img
                :src="captchaUrl"
                alt="验证码"
                class="captcha-img"
                @click="refreshCaptcha"
                @load="imgLoaded = true"
              />
            </div>
            <input
              id="captcha"
              v-model="captcha"
              class="captcha-input"
              type="text"
              maxlength="6"
              placeholder="验证码"
            />
          </div>
        </div>
        <div class="actions">
          <a class="forgot-password" href="#">忘记密码</a>
          <div class="button-group">
            <button class="btn login-btn" type="submit">登录</button>
            <button
              class="btn register-btn"
              type="button"
              @click="handleRegister"
            >
              注册
            </button>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

// 表单数据
const username = ref("");
const password = ref("");
const captcha = ref("");

// 验证码相关
const captchaKey = ref("");
const captchaUrl = ref("");
const captchaImgLoaded = ref(false);

// 是否在注册页面（预留功能）
const isRegistrationPage = ref(false);

// 统一刷新验证码函数
const refreshCaptcha = async () => {
  captchaImgLoaded.value = false;
  try {
    const response = await fetch("https://wanqiu.cloudns.ch:4433/api/captcha");

    if (!response.ok) {
      throw new Error(`HTTP error! 状态码: ${response.status}`);
    }

    const blob = await response.blob();
    const key = response.headers.get("x-captcha-key") || "";

    captchaKey.value = key;
    captchaUrl.value = URL.createObjectURL(blob);
  } catch (error) {
    console.error("验证码加载失败:", error);
    window.alert("验证码加载失败，请稍后再试");
  }
};

// 登录事件
const handleLogin = async () => {
  if (!username.value || !password.value || !captcha.value) {
    window.alert("请填写所有字段");
    return;
  }

  if (!captchaKey.value) {
    await refreshCaptcha();
  }

  try {
    const { data } = await axios.post(
      "https://wanqiu.cloudns.ch:4433/api/captcha/verify",
      {
        captchaCode: captcha.value,
        captchaKey: captchaKey.value,
      }
    );

    if (data.code === 0) {
      notifySuccess(data.message || "登录成功");
      // TODO: 后续跳转或存储 token 等操作
    } else {
      window.alert(data.message || "验证码错误");
      captcha.value = "";
      await refreshCaptcha();
    }
  } catch (error) {
    console.error("登录请求失败:", error);
    window.alert("请求失败，请检查网络连接");
    await refreshCaptcha();
  }
};

// 注册点击（预留）
const handleRegister = () => {
  notifySuccess("注册功能开发中…");
  isRegistrationPage.value = !isRegistrationPage.value;
};

// 页面加载时先请求验证码
onMounted(() => {
  refreshCaptcha();
});

// 简易提示函数（可换成 Toast、ElMessage 等）
function notifySuccess(msg) {
  window.alert(msg);
}
</script>

<style scoped>
/* 通用基础 */
* {
  box-sizing: border-box;
}

html {
  font-size: 16px;
}

body {
  font-family: Arial, sans-serif;
}

/* 页面布局容器 */
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  padding: 2rem;
}

.login-box {
  width: 100%;
  max-width: 30rem;
  padding: 2rem;
  background-color: #fff;
  border-radius: 0.8rem;
  box-shadow: 0 0 0.8rem rgba(0, 0, 0, 0.1);
  animation: fadeIn 0.5s ease-in-out;
  transition: all 0.3s ease;
  overflow: hidden;
}

/* 标题 */
.login-title {
  font-size: 1.5rem;
  text-align: center;
  margin-bottom: 2rem;
  user-select: none;
}

/* 表单输入组 */
.input-group {
  margin-bottom: 1.25rem;
}

.input-group label {
  display: block;
  margin-bottom: 0.375rem;
  font-size: 0.875rem;
  color: #333;
}

/* 输入框包装 */
.input-wrapper {
  position: relative;
}

.input-wrapper .icon {
  position: absolute;
  top: 50%;
  left: 0.5rem;
  transform: translateY(-50%);
  font-size: 1.2rem;
  color: #aaa;
}

.input-wrapper input {
  width: 100%;
  padding: 0.625rem 0.75rem 0.625rem 2.75rem;
  font-size: 1rem;
  color: #222;
  background: #fff;
  border: 1px solid #ccc;
  border-radius: 0.5rem;
  outline: none;
  transition: all 0.25s ease;
}

.input-wrapper input:focus {
  border-color: #5a67d8;
  box-shadow: 0 0 0 3px rgba(90, 103, 216, 0.2);
}

/* 验证码区域 */
.captcha-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1.5rem;
}

.captcha-container {
  position: relative;
  width: 10rem;
  height: 3.75rem;
}

.captcha-img {
  width: 100%;
  height: 100%;
  margin: auto;
  border-radius: 0.5rem;
  object-fit: cover;
  background: linear-gradient(135deg, #f2f2f2, #e0e0e0);
  cursor: pointer;
  transition: filter 0.4s ease-in-out, opacity 0.4s ease, transform 0.3s ease;
}

.captcha-img.is-loading,
.captcha-img:active {
  transform: scale(0.95);
  opacity: 0.6;
}

.captcha-wrapper input {
  width: 7.5rem;
  padding: 0.625rem 0.75rem;
  font-size: 1rem;
  color: #222;
  background: #fff;
  border: 1px solid #ccc;
  border-radius: 0.5rem;
  outline: none;
  transition: all 0.25s ease;
}

.captcha-wrapper input:focus {
  border-color: #5a67d8;
  box-shadow: 0 0 0 3px rgba(90, 103, 216, 0.2);
}

/* 忘记密码链接 */
.forgot-password {
  font-size: 0.8125rem;
  color: #666;
  text-decoration: none;
  display: block;
  text-align: right;
  margin-top: 0.375rem;
  padding-bottom: 1px;
  border-bottom: 2px solid black;
  line-height: 1.5;
  transition: border-color 0.2s ease, color 0.2s ease;
}

.forgot-password:hover {
  color: #000;
  border-bottom-color: #000;
}

/* 按钮组 */
.button-group {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
  margin-top: 1.5rem;
}

.btn {
  flex: 1;
  padding: 0.75rem;
  font-size: 1rem;
  font-weight: bold;
  border-radius: 0.5rem;
  border: none;
  cursor: pointer;
  transition: all 0.3s ease;
}

.login-btn {
  background: #000;
  color: #fff;
}

.login-btn:hover {
  background: #222;
  transform: scale(1.03);
}

.register-btn {
  background: #f1f1f1;
  color: #333;
  border: 1px solid #ddd;
}

.register-btn:hover {
  background: #e2e2e2;
  transform: scale(1.03);
}

/* 图标伪元素 */
.user-icon::before {
  content: "👤";
}

.lock-icon::before {
  content: "🔒";
}

.Email-Verification-Code::before {
  content: "🛡️";
}

/* 动画 */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Vue 过渡动画 */
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: height 0.3s ease, opacity 0.3s ease;
}

.fade-slide-enter-from,
.fade-slide-leave-to {
  height: 0;
  opacity: 0;
}
</style>
