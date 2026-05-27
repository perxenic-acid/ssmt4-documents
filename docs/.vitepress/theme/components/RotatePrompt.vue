<script setup lang="ts">
// 纯 CSS 方案：通过媒体查询控制显隐，无需 JS 状态
</script>

<template>
  <div class="rotate-prompt-overlay">
    <div class="rotate-prompt-card">
      <div class="rotate-icon">
        <svg viewBox="0 0 100 100" width="80" height="80">
          <!-- 手机外框 -->
          <rect x="25" y="10" width="50" height="80" rx="8" fill="none" stroke="currentColor" stroke-width="3"/>
          <rect x="40" y="82" width="20" height="2" rx="1" fill="currentColor"/>
          <!-- 旋转箭头 -->
          <path d="M55 50 Q75 45 80 35" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round"/>
          <path d="M72 42 L80 35 L78 28" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M45 50 Q25 55 20 65" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round"/>
          <path d="M28 58 L20 65 L22 72" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </div>
      <p class="rotate-text">请将手机横屏观看</p>
      <p class="rotate-sub">Please rotate your device to landscape</p>
    </div>
  </div>
</template>

<style scoped>
.rotate-prompt-overlay {
  display: none;
  position: fixed;
  inset: 0;
  z-index: 99999;
  background: linear-gradient(135deg, #0b0c15 0%, #1a1025 50%, #0b0c15 100%);
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

/* 手机竖屏 + 窄屏时显示 */
@media screen and (orientation: portrait) and (max-width: 768px) {
  .rotate-prompt-overlay {
    display: flex;
  }
}

.rotate-prompt-card {
  text-align: center;
  animation: rotatePulse 2s ease-in-out infinite;
}

.rotate-icon {
  color: #ff0080;
  margin-bottom: 24px;
  animation: rotateHint 2.5s ease-in-out infinite;
}

@keyframes rotateHint {
  0% { transform: rotate(0deg); }
  25% { transform: rotate(-90deg); }
  50% { transform: rotate(-90deg); }
  75% { transform: rotate(0deg); }
  100% { transform: rotate(0deg); }
}

@keyframes rotatePulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.8; }
}

.rotate-text {
  font-size: 22px;
  font-weight: 700;
  color: #ffffff;
  margin: 0 0 8px;
  letter-spacing: 2px;
}

.rotate-sub {
  font-size: 13px;
  color: rgba(255, 255, 255, 0.45);
  margin: 0;
  letter-spacing: 1px;
}
</style>
