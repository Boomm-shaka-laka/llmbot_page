<template>
  <div class="app">
    <!-- 导航栏 -->
    <header class="navbar">
      <div class="navbar-left">
        <span class="logo">LLMBOAT</span>
      </div>
      <div class="navbar-right">
        <button @click="toggleTheme" class="theme-toggle-btn">
          {{ isDark ? '☀️ 亮色' : '🌙 暗色' }}
        </button>
      </div>
    </header>

    <!-- 主体布局 -->
    <div class="main-layout">
      <!-- 侧边栏 -->
      <aside class="sidebar">
        <div class="sidebar-group">
          <label>船舶类型</label>
          <select v-model="selectedShipType" class="flat-select">
            <option value="" disabled>请选择类型</option>
            <option value="散货船">散货船</option>
            <option value="钢制海船">钢制海船</option>
            <option value="LNG船">LNG船</option>
            <option value="渔船">渔船</option>
            <option value="游船">游船</option>
          </select>
        </div>

        <div class="sidebar-group" v-if="selectedShipType">
          <label>船体型号</label>
          <select v-model="selectedModel" class="flat-select">
            <option value="" disabled>请选择型号</option>
            <option
              v-for="model in modelOptions[selectedShipType]"
              :key="model"
              :value="model"
            >
              {{ model }}
            </option>
          </select>
        </div>

        <div class="sidebar-group">
          <label>选择地图</label>
          <select v-model="selectedMap" class="flat-select">
            <option value="" disabled>请选择地图</option>
            <option value="东钱湖">东钱湖</option>
            <option value="千岛湖">千岛湖</option>
          </select>
        </div>

        <div class="button-group">
          <button
            @click="showInfo"
            class="action-btn info-btn"
            :disabled="!selectedShipType || !selectedModel"
          >
            显示信息
          </button>
          <button @click="goToSystem" class="action-btn console-btn">
            进入控制台
          </button>
        </div>
      </aside>
    </div>

    <!-- 信息卡片遮罩层（模态框） -->
    <transition name="fade-bg">
      <div v-if="showingInfo" class="modal-overlay" @click="closeInfo"></div>
    </transition>

    <transition name="slide-scale">
      <div v-if="showingInfo" class="info-card-modal">
        <button class="close-btn" @click="closeInfo">×</button>
        <div class="info-header">
          <h2>{{ selectedShipType }} · {{ selectedModel }}</h2>
        </div>
        <div class="info-body">
          <div class="info-image-placeholder"></div>
          <div class="info-text">
            <p>
              当前选择的船舶为 <strong>{{ selectedShipType }}</strong>，型号
              <strong>{{ selectedModel }}</strong>，部署于 <strong>{{ selectedMap }}</strong>。
            </p>
            <p>
              该船型具备先进的自动避障系统、AIS广播模块与智能路径规划能力，适用于内湖/近海复杂水域环境。
            </p>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, onUnmounted } from 'vue'

const modelOptions = {
  散货船: ['001', '002', '003'],
  钢制海船: ['111', '112', '113'],
  LNG船: ['001', '002', '003'],
  渔船: ['211', '212', '213'],
  游船: ['311', '312', '313']
}

const selectedShipType = ref('')
const selectedModel = ref('')
const selectedMap = ref('')
const showingInfo = ref(false)
const isDark = ref(false)

watch(selectedShipType, (newVal) => {
  if (newVal && modelOptions[newVal] && modelOptions[newVal].length > 0) {
    selectedModel.value = modelOptions[newVal][0]
  } else {
    selectedModel.value = ''
  }
})

function toggleTheme() {
  isDark.value = !isDark.value
  document.documentElement.classList.toggle('dark', isDark.value)
}

function showInfo() {
  if (selectedShipType.value && selectedModel.value) {
    showingInfo.value = true
  }
}

function closeInfo() {
  showingInfo.value = false
}

function handleEscape(e) {
  if (e.key === 'Escape' && showingInfo.value) {
    closeInfo()
  }
}

function goToSystem() {
  window.open('https://llmboat.sjturz.com/', '_blank')
}

onMounted(() => {
  window.addEventListener('keydown', handleEscape)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleEscape)
})
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, sans-serif;
}


.app {
  position: relative;
  width: 100vw;
  min-height: 100vh;
  background: url('/images/background2.png') no-repeat;
  background-size: 90% auto; /* 自定义缩放比例 */
  background-position: 0% 50%; /* 居中显示 */
}


.app::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.4);
  transition: background 0.4s ease;
  z-index: 1; /* 在背景图之上，内容之下 */
}

html.dark .app::before {
  background: rgba(0, 0, 0, 0.7);
}

.navbar {
  width: 100%;
  background: #0d1b3d;
  color: white;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 20px;
  height: 60px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  position: fixed;
  top: 0;
  left: 0;
  z-index: 10;
  transition: all 0.3s ease;
  /* 添加底部边框 */
  border-bottom: 1px solid rgba(255, 255, 255, 0.1); /* 调整颜色和透明度以适应你的设计 */
}

html.dark .navbar {
  background: #0a1428;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.4);
}

.navbar-left .logo {
  font-size: 1.5rem;
  font-weight: 700;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(90deg, #5d3aee, #a54ed6);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  letter-spacing: 1px;
}

.navbar-right .theme-toggle-btn {
  background: rgba(255, 255, 255, 0.15);
  border: none;
  color: white;
  padding: 6px 12px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 500;
  font-size: 0.95rem;
  transition: all 0.2s ease;
}

.navbar-right .theme-toggle-btn:hover {
  background: rgba(255, 255, 255, 0.25);
  transform: scale(1.05);
}

.main-layout {
  display: flex;
  position: absolute;
  top: 60px;
  left: 280px;
  width: calc(100vw - 280px);
  height: calc(100vh - 60px);
  /* 或者用 margin-left: 280px; 也可以，但要确保 .app 没有 padding */
}

.sidebar {
  position: fixed;
  top: 50px;
  left: 0;
  bottom: 0;
  width: 320px;
  background: #0d1b3d;
  color: white;
  padding: 24px 20px;
  display: flex;
  flex-direction: column;
  gap: 22px;
  overflow-y: auto;
  z-index: 9;
  box-shadow: 2px 0 12px rgba(0, 0, 0, 0.15);
}

html.dark .sidebar {
  background: #121e32;
}

.sidebar-group label {
  display: block;
  font-size: 14px;
  font-weight: 600;
  margin-bottom: 8px;
  color: #e0e0ff;
}

.flat-select {
  width: 100%;
  padding: 12px 16px;
  font-size: 15px;
  border: none;
  border-radius: 14px;
  background: #f8f9fa;
  color: #212529;
  outline: none;
  transition: all 0.25s cubic-bezier(0.34, 1.56, 0.64, 1);
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.06);
  appearance: none;
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16'%3e%3cpath fill='none' stroke='%23555' stroke-linecap='round' stroke-linejoin='round' stroke-width='2' d='m2 5 6 6 6-6'/%3e%3c/svg%3e");
  background-repeat: no-repeat;
  background-position: right 12px center;
  background-size: 14px;
  cursor: pointer;
}

.flat-select:hover {
  box-shadow: 0 3px 8px rgba(0, 0, 0, 0.1);
}

.flat-select:focus {
  box-shadow: 0 0 0 3px rgba(25, 118, 210, 0.3), 0 3px 10px rgba(0, 0, 0, 0.12);
  transform: translateY(-1px);
}

html.dark :deep(.flat-select) {
  background: #2d2d2d;
  color: #f0f0f0;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16'%3e%3cpath fill='none' stroke='%23ffffff' stroke-linecap='round' stroke-linejoin='round' stroke-width='2' d='m2 5 6 6 6-6'/%3e%3c/svg%3e");
}

html.dark :deep(.flat-select:hover) {
  box-shadow: 0 3px 8px rgba(0, 0, 0, 0.25);
}

html.dark :deep(.flat-select:focus) {
  box-shadow: 0 0 0 3px rgba(30, 136, 229, 0.4), 0 3px 10px rgba(0, 0, 0, 0.2);
}

.button-group {
  display: flex;
  flex-direction: column;
  gap: 14px;
  margin-top: 16px;
  padding-top: 12px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.action-btn {
  padding: 13px 20px;
  border: none;
  border-radius: 16px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
  letter-spacing: 0.6px;
  box-shadow: 0 3px 8px rgba(0, 0, 0, 0.12);
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 52px;
  color: white;
}

.action-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 5px 14px rgba(0, 0, 0, 0.18);
}

.action-btn:active:not(:disabled) {
  transform: translateY(0);
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
}

.info-btn {
  background: linear-gradient(135deg, #1976d2, #1565c0);
}

.console-btn {
  background: linear-gradient(135deg, #4caf50, #43a047);
}

.info-btn:disabled {
  background: #e0e0e0;
  color: #9e9e9e;
  cursor: not-allowed;
  transform: none;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.06);
}

html.dark .info-btn:disabled {
  background: #444;
  color: #888;
}

html.dark .info-btn {
  background: linear-gradient(135deg, #1e88e5, #1976d2);
}

html.dark .console-btn {
  background: linear-gradient(135deg, #66bb6a, #4caf50);
}

.placeholder-text {
  color: #777;
  font-size: 16px;
  text-align: center;
  max-width: 500px;
  line-height: 1.6;
}

html.dark .placeholder-text {
  color: #aaa;
}

/* ===== 模态卡片 ===== */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.4);
  z-index: 999;
}

html.dark .modal-overlay {
  background: rgba(0, 0, 0, 0.6);
}

.info-card-modal {
  position: fixed;
  top: 15vh; /* 居中靠上 */
  left: 50%;
  transform: translateX(-50%);
  width: 90%;
  max-width: 800px; /* 更宽 */
  max-height: 80vh;
  background: rgba(255, 255, 255, 0.96);
  backdrop-filter: blur(12px);
  border-radius: 20px;
  padding: 32px;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.25);
  z-index: 1000;
  overflow: auto;
}

html.dark .info-card-modal {
  background: rgba(28, 28, 28, 0.94);
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.5);
  color: #eee;
}

.close-btn {
  position: absolute;
  top: 18px;
  right: 18px;
  background: none;
  border: none;
  font-size: 26px;
  color: #999;
  cursor: pointer;
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: all 0.2s ease;
}

.close-btn:hover {
  color: #e53935;
  background: rgba(0, 0, 0, 0.06);
}

html.dark .close-btn {
  color: #bbb;
}

html.dark .close-btn:hover {
  color: #ff6b6b;
  background: rgba(255, 255, 255, 0.1);
}

.info-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 28px;
  padding-bottom: 18px;
  border-bottom: 1px solid #eee;
}

html.dark .info-header {
  border-bottom-color: #444;
}

.info-header h2 {
  font-size: 24px;
  font-weight: 700;
  color: #1976d2;
}

html.dark .info-header h2 {
  color: #4fc3f7;
}

.map-tag {
  background: #e3f2fd;
  color: #0d47a1;
  padding: 5px 14px;
  border-radius: 20px;
  font-size: 14px;
  font-weight: 600;
}

html.dark .map-tag {
  background: #1a237e;
  color: #bbdefb;
}

.info-body {
  display: flex;
  gap: 28px;
  align-items: flex-start;
}

.info-image-placeholder {
  width: 160px;
  height: 160px;
  background: linear-gradient(135deg, #4caf50, #81c784);
  border-radius: 16px;
  flex-shrink: 0;
}

.info-text {
  flex: 1;
  font-size: 16px;
  line-height: 1.7;
  color: #333;
}

html.dark .info-text {
  color: #ddd;
}

.info-text strong {
  color: #1976d2;
  font-weight: 600;
}

html.dark .info-text strong {
  color: #4fc3f7;
}

/* ===== 动画 ===== */
.fade-bg-enter-active,
.fade-bg-leave-active {
  transition: opacity 0.3s ease;
}
.fade-bg-enter-from,
.fade-bg-leave-to {
  opacity: 0;
}

.slide-scale-enter-active {
  transition: all 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
}
.slide-scale-leave-active {
  transition: all 0.3s ease;
}
.slide-scale-enter-from {
  opacity: 0;
  transform: translate(-50%, -40px) scale(0.85);
}
.slide-scale-leave-to {
  opacity: 0;
  transform: translate(-50%, 20px) scale(0.9);
}
</style>