<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'
import { withBase } from 'vitepress'

const audioEl = ref<HTMLAudioElement | null>(null)
const isPlaying = ref(false)
const blockedByPolicy = ref(false)
const volume = ref(0.3)

const musicSrc = withBase('/Background.ogg')

const buttonTitle = computed(() => {
  if (blockedByPolicy.value) return '浏览器限制自动播放，点击后开始播放'
  return isPlaying.value ? '暂停背景音乐' : '播放背景音乐'
})

const playNow = async () => {
  const el = audioEl.value
  if (!el) return
  try {
    await el.play()
    isPlaying.value = true
    blockedByPolicy.value = false
  } catch {
    isPlaying.value = false
    blockedByPolicy.value = true
  }
}

const pauseNow = () => {
  const el = audioEl.value
  if (!el) return
  el.pause()
  isPlaying.value = false
}

const setVolume = (value: number) => {
  const normalized = Math.min(1, Math.max(0, value))
  volume.value = normalized
  const el = audioEl.value
  if (el) {
    el.volume = normalized
  }
  localStorage.setItem('bgm-volume', String(normalized))
}

const onVolumeInput = (event: Event) => {
  const target = event.target as HTMLInputElement
  setVolume(Number(target.value))
}

const togglePlay = () => {
  if (isPlaying.value) {
    pauseNow()
  } else {
    void playNow()
  }
}

const resumeOnGesture = () => {
  void playNow()
}

onMounted(() => {
  const saved = Number(localStorage.getItem('bgm-volume'))
  if (!Number.isNaN(saved)) {
    setVolume(saved)
  }

  if (audioEl.value) {
    audioEl.value.volume = volume.value
  }

  // Direct src playback uses browser progressive loading (play while buffering).
  void playNow()
  window.addEventListener('pointerdown', resumeOnGesture, { once: true })
  window.addEventListener('keydown', resumeOnGesture, { once: true })
  window.addEventListener('touchstart', resumeOnGesture, { once: true })
})

onBeforeUnmount(() => {
  window.removeEventListener('pointerdown', resumeOnGesture)
  window.removeEventListener('keydown', resumeOnGesture)
  window.removeEventListener('touchstart', resumeOnGesture)
})
</script>

<template>
  <div class="music-control" :class="{ blocked: blockedByPolicy }">
    <button class="music-toggle" :title="buttonTitle" @click="togglePlay">
      <span class="icon" aria-hidden="true">{{ isPlaying ? '||' : '>' }}</span>
      <span class="label">BGM</span>
    </button>

    <input
      class="volume-slider"
      type="range"
      min="0"
      max="1"
      step="0.01"
      :value="volume"
      @input="onVolumeInput"
      aria-label="背景音乐音量"
      title="背景音乐音量"
    />

    <audio
      ref="audioEl"
      :src="musicSrc"
      preload="none"
      loop
      @play="isPlaying = true"
      @pause="isPlaying = false"
    />
  </div>
</template>

<style scoped>
.music-control {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-left: 12px;
  padding-left: 12px;
  border-left: 1px solid rgba(255, 0, 128, 0.15);
  height: 28px;
  transition: border-color 0.3s;
}

.music-toggle {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  background: rgba(255, 255, 255, 0.08);
  border: 1px solid rgba(0, 0, 0, 0.08);
  border-radius: 999px;
  cursor: pointer;
  color: var(--vp-c-text-1);
  padding: 4px 10px;
  font-size: 12px;
  line-height: 1;
  letter-spacing: 0.2px;
  transition: all 0.25s ease;
}

.dark .music-toggle {
  background: rgba(255, 255, 255, 0.06);
  border: 1px solid rgba(255, 255, 255, 0.08);
}

.music-toggle:hover {
  background: rgba(255, 0, 128, 0.1) !important;
  border-color: rgba(255, 0, 128, 0.3) !important;
  color: #ff0080 !important;
  transform: translateY(-1px) scale(1.03);
  box-shadow: 0 2px 8px rgba(255, 0, 128, 0.15);
}

.dark .music-toggle:hover {
  background: rgba(255, 0, 128, 0.15) !important;
}

.label {
  display: inline-block;
  font-weight: 600;
  transition: color 0.25s;
}

.music-toggle:hover .label {
  color: #ff0080;
}

.icon {
  width: 10px;
  text-align: center;
  font-size: 11px;
  transition: transform 0.3s;
}

.music-toggle:hover .icon {
  transform: scale(1.2);
}

/* ---- 自定义音量滑块 ---- */
.volume-slider {
  -webkit-appearance: none;
  appearance: none;
  width: 80px;
  height: 4px;
  border-radius: 2px;
  background: rgba(0, 0, 0, 0.1);
  cursor: pointer;
  outline: none;
  transition: background 0.3s;
}

.dark .volume-slider {
  background: rgba(255, 255, 255, 0.1);
}

.volume-slider:hover {
  background: rgba(255, 0, 128, 0.15);
}

.dark .volume-slider:hover {
  background: rgba(255, 0, 128, 0.2);
}

/* 滑块轨道 - WebKit */
.volume-slider::-webkit-slider-runnable-track {
  height: 4px;
  border-radius: 2px;
  background: transparent;
}

/* 滑块拇指 - WebKit */
.volume-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background: #ff0080;
  border: 2px solid #fff;
  box-shadow: 0 1px 4px rgba(255, 0, 128, 0.4);
  cursor: pointer;
  margin-top: -5px;
  transition: all 0.2s;
}

.volume-slider::-webkit-slider-thumb:hover {
  transform: scale(1.2);
  box-shadow: 0 0 10px rgba(255, 0, 128, 0.6);
}

.dark .volume-slider::-webkit-slider-thumb {
  border-color: #1a1a2e;
  box-shadow: 0 1px 4px rgba(255, 0, 128, 0.6);
}

/* 滑块轨道 - Firefox */
.volume-slider::-moz-range-track {
  height: 4px;
  border-radius: 2px;
  background: transparent;
  border: none;
}

/* 滑块拇指 - Firefox */
.volume-slider::-moz-range-thumb {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background: #ff0080;
  border: 2px solid #fff;
  box-shadow: 0 1px 4px rgba(255, 0, 128, 0.4);
  cursor: pointer;
}

.dark .volume-slider::-moz-range-thumb {
  border-color: #1a1a2e;
}

.music-control.blocked .music-toggle {
  border-color: rgba(245, 158, 11, 0.4) !important;
}

.music-control.blocked .music-toggle:hover {
  border-color: rgba(245, 158, 11, 0.6) !important;
  color: #f59e0b !important;
}

audio {
  display: none;
}

@media (max-width: 768px) {
  .music-control {
    display: none;
  }
}
</style>