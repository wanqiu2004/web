<template>
  <div class="app">
    <!-- 顶部导航栏 -->
    <header class="navbar" role="banner">
      <div class="container nav-container">
        <div class="logo" aria-label="ToC Proxy 首页">ToC Proxy</div>
        <nav class="nav-links" role="navigation" aria-label="主菜单">
          <a href="#features" @click="smoothScroll">功能</a>
          <a href="#pricing" @click="smoothScroll">价格</a>
          <a href="#faq" @click="smoothScroll">FAQ</a>
          <a href="#contact" @click="smoothScroll">联系我们</a>
        </nav>
      </div>
    </header>

    <!-- 主视觉区 -->
    <section class="hero" id="hero" aria-labelledby="hero-title">
      <div class="container">
        <h1 id="hero-title">安全、快速、全球连接</h1>
        <p>ToC Proxy 提供企业级网络代理解决方案，轻松访问全球资源。</p>
        <button type="button" class="btn btn-primary" @click="scrollToSection('#pricing')">
          立即开始
        </button>
      </div>
    </section>

    <!-- 功能区 -->
    <section id="features" class="section features" aria-labelledby="features-title">
      <div class="container">
        <h2 id="features-title" class="section-title">核心功能</h2>
        <div class="grid-3">
          <div class="card feature-card">
            <h3>极速节点</h3>
            <p>覆盖全球数十个国家，低延迟高速连接。</p>
          </div>
          <div class="card feature-card">
            <h3>隐私保护</h3>
            <p>零日志政策，数据加密，保障您的上网安全。</p>
          </div>
          <div class="card feature-card">
            <h3>多平台支持</h3>
            <p>支持 Windows、Mac、iOS、Android 等多个平台。</p>
          </div>
        </div>
      </div>
    </section>

    <!-- 价格区 -->
    <section id="pricing" class="section pricing" aria-labelledby="pricing-title">
      <div class="container">
        <h2 id="pricing-title" class="section-title">价格计划</h2>
        <div class="grid-3">
          <!-- 入门版 -->
          <div class="card price-card">
            <h3>入门版</h3>
            <p class="price">¥19<span>/月</span></p>
            <ul aria-label="入门版包含功能">
              <li>5 个节点</li>
              <li>无限流量</li>
              <li>基础客服</li>
            </ul>
            <button type="button" class="btn btn-secondary">选择</button>
          </div>

          <!-- 专业版（推荐） -->
          <div class="card price-card price-card-highlighted">
            <span class="badge">最受欢迎</span>
            <h3>专业版</h3>
            <p class="price">¥49<span>/月</span></p>
            <ul aria-label="专业版包含功能">
              <li>20 个节点</li>
              <li>优先通道</li>
              <li>24/7 专属支持</li>
            </ul>
            <button type="button" class="btn btn-primary">选择</button>
          </div>

          <!-- 企业版 -->
          <div class="card price-card">
            <h3>企业版</h3>
            <p class="price">¥199<span>/月</span></p>
            <ul aria-label="企业版包含功能">
              <li>不限节点</li>
              <li>独享带宽</li>
              <li>API 接入</li>
            </ul>
            <button type="button" class="btn btn-secondary">定制咨询</button>
          </div>
        </div>
      </div>
    </section>

    <!-- 常见问题 -->
    <section id="faq" class="section faq" aria-labelledby="faq-title">
      <div class="container">
        <h2 id="faq-title" class="section-title">常见问题</h2>
        <div class="faq-list">
          <div
            v-for="(item, index) in faqs"
            :key="index"
            class="faq-item"
            :aria-labelledby="`faq-q-${index}`"
          >
            <h4 :id="`faq-q-${index}`" class="faq-question">{{ item.q }}</h4>
            <p class="faq-answer">{{ item.a }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- 页脚 -->
    <footer class="footer" role="contentinfo">
      <div class="container">
        <p>© 2025 ToC Proxy Inc. 版权所有 | ICP证 xxxxx</p>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { onMounted } from 'vue';

// 常见问题数据
const faqs = [
  { q: '你们支持哪些设备？', a: '支持 Windows、macOS、Android 和 iOS。' },
  { q: '如何确保我的隐私？', a: '我们采用零日志策略，并提供多重加密通道。' },
  { q: '能否开具发票？', a: '支持企业用户提供正规发票服务。' }
];

// 平滑滚动函数
const smoothScroll = (e) => {
  e.preventDefault();
  const target = e.target.getAttribute('href');
  scrollToSection(target);
};

const scrollToSection = (selector) => {
  const el = document.querySelector(selector);
  if (el) {
    el.scrollIntoView({ behavior: 'smooth' });
  }
};

// DOM 加载完成后绑定锚点点击事件（兼容非 Vue 控件）
onMounted(() => {
  // 可选：为所有外部锚点添加平滑滚动
  document.querySelectorAll('a[href^="#"]').forEach(anchor => {
    anchor.addEventListener('click', function (e) {
      if (this.getAttribute('href').length > 1) {
        e.preventDefault();
        const target = this.getAttribute('href');
        scrollToSection(target);
      }
    });
  });
});
</script>

<style scoped>
/* ==================== */
/* 1. 全局变量与重置 */
/* ==================== */

:root {
  --color-primary: #0066ff;
  --color-primary-hover: #004fc1;
  --color-secondary: #e2e8f0;
  --color-text: #1a202c;
  --color-bg: #ffffff;
  --color-light-bg: #f9fafb;
  --color-footer-bg: #1a202c;
  --color-footer-text: #cbd5e0;

  --radius-md: 0.75rem;
  --shadow-sm: 0 4px 12px rgba(0, 0, 0, 0.05);
  --shadow-lg: 0 0 15px rgba(0, 0, 0, 0.05);
  --transition: 0.3s ease-in-out;

  --font-family: 'Segoe UI', -apple-system, BlinkMacSystemFont, 'Roboto', sans-serif;
  --font-size-base: 1rem;
  --line-height: 1.6;
}

/* 基础重置与默认样式 */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: var(--font-family);
  color: var(--color-text);
  background-color: var(--color-bg);
  line-height: var(--line-height);
}

/* ==================== */
/* 2. 布局与容器 */
/* ==================== */

.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.container {
  width: 90%;
  max-width: 1200px;
  margin-inline: auto;
  padding-inline: 1rem;
}

.section {
  padding-block: 4rem;
}

/* ==================== */
/* 3. 导航栏 */
/* ==================== */

.navbar {
  background-color: var(--color-bg);
  border-bottom: 1px solid var(--color-secondary);
  padding-block: 1rem;
  position: sticky;
  top: 0;
  z-index: 1000;
  backdrop-filter: blur(8px); /* 轻微毛玻璃效果 */
  -webkit-backdrop-filter: blur(8px);
}

.nav-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--color-primary);
}

.nav-links {
  display: flex;
  gap: 1.5rem;
}

.nav-links a {
  color: var(--color-text);
  text-decoration: none;
  font-weight: 500;
  transition: color var(--transition);
  padding: 0.5rem 0;
  position: relative;
}

.nav-links a:hover,
.nav-links a:focus {
  color: var(--color-primary);
}

.nav-links a:focus {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
  border-radius: 4px;
}

/* ==================== */
/* 4. Hero 区域 */
/* ==================== */

.hero {
  background: linear-gradient(to right, #ebf4ff, #e3f2fd);
  text-align: center;
  padding-block: 6rem 4rem;
}

.hero h1 {
  font-size: clamp(2rem, 5vw, 2.8rem); /* 响应式字体 */
  margin-bottom: 1rem;
  color: var(--color-text);
}

.hero p {
  font-size: 1.2rem;
  max-width: 600px;
  margin: 0 auto 2rem;
  opacity: 0.9;
}

/* ==================== */
/* 5. 按钮系统（统一组件化） */
/* ==================== */

.btn {
  display: inline-block;
  padding: 0.75rem 2rem;
  border: none;
  border-radius: var(--radius-md);
  cursor: pointer;
  font-size: var(--font-size-base);
  font-weight: 500;
  transition: all var(--transition);
  text-decoration: none;
}

.btn-primary {
  background-color: var(--color-primary);
  color: #fff;
}

.btn-primary:hover,
.btn-primary:focus {
  background-color: var(--color-primary-hover);
  transform: translateY(-2px);
}

.btn-primary:focus {
  outline: 2px solid rgba(0, 102, 255, 0.4);
}

.btn-secondary {
  background-color: var(--color-secondary);
  color: var(--color-text);
}

.btn-secondary:hover,
.btn-secondary:focus {
  background-color: #d1dce8;
}

.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

/* ==================== */
/* 6. 卡片与网格系统 */
/* ==================== */

.grid-3 {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 2rem;
  margin-top: 2rem;
}

.card {
  background-color: #fff;
  padding: 2rem;
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-sm);
  transition: transform var(--transition), box-shadow var(--transition);
}

.card:hover {
  transform: translateY(-6px);
  box-shadow: var(--shadow-lg);
}

/* ==================== */
/* 7. 功能区 */
/* ==================== */

.features {
  background-color: var(--color-light-bg);
}

.feature-card h3 {
  margin-bottom: 1rem;
  font-size: 1.3rem;
}

.feature-card p {
  color: var(--color-text);
  opacity: 0.8;
}

/* ==================== */
/* 8. 价格区 */
/* ==================== */

.pricing {
  background-color: var(--color-bg);
}

.price-card {
  position: relative;
  text-align: center;
}

.price-card-highlighted {
  border: 2px solid var(--color-primary);
  padding-top: 2.3rem;
  padding-bottom: 2.3rem;
}

.badge {
  position: absolute;
  top: -12px;
  right: -2px;
  background-color: var(--color-primary);
  color: white;
  font-size: 0.8rem;
  padding: 0.4rem 0.8rem;
  border-radius: 20px;
  font-weight: 600;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

.price {
  font-size: 2rem;
  font-weight: 700;
  margin: 1rem 0;
  color: var(--color-text);
}

.price span {
  font-size: 0.85em;
  font-weight: 400;
  color: #666;
}

.price-card ul {
  list-style: none;
  padding: 0;
  margin: 1.5rem 0;
}

.price-card li {
  margin: 0.6rem 0;
  color: var(--color-text);
  opacity: 0.8;
}

/* ==================== */
/* 9. 常见问题 */
/* ==================== */

.faq {
  background-color: var(--color-light-bg);
}

.faq-list {
  max-width: 800px;
  margin: 2rem auto 0;
}

.faq-item {
  background-color: #fff;
  padding: 1.5rem;
  border-radius: var(--radius-md);
  margin-bottom: 1.2rem;
  box-shadow: var(--shadow-sm);
}

.faq-question {
  font-size: 1.15rem;
  margin-bottom: 0.6rem;
  color: var(--color-text);
}

.faq-answer {
  color: #555;
  line-height: 1.7;
}

/* ==================== */
/* 10. 页脚 */
/* ==================== */

.footer {
  background-color: var(--color-footer-bg);
  color: var(--color-footer-text);
  padding-block: 2rem;
  font-size: 0.9rem;
  margin-top: auto;
}

.footer p {
  opacity: 0.8;
}

/* ==================== */
/* 11. 响应式适配 */
/* ==================== */

@media (max-width: 768px) {
  .nav-links {
    gap: 1rem;
  }

  .nav-links a {
    font-size: 0.95rem;
  }

  .hero h1 {
    font-size: 2rem;
  }

  .hero p {
    font-size: 1rem;
  }

  .section {
    padding-block: 3rem 2.5rem;
  }

  .grid-3 {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }

  .price-card-highlighted {
    transform: scale(1.03);
  }

  .btn {
    padding: 0.6rem 1.5rem;
    font-size: 0.95rem;
  }
}

@media (max-width: 480px) {
  .container {
    width: 95%;
  }

  .hero {
    padding-block: 4rem 3rem;
  }

  .section-title {
    font-size: 1.6rem;
  }

  .faq-item {
    padding: 1.2rem;
  }
}
</style>