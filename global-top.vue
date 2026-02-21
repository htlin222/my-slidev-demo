<script setup>
import { computed } from 'vue'
import { useNav } from '@slidev/client'

const { currentSlideNo, go } = useNav()

const chapters = [
  { label: '簡介', start: 2, end: 4 },
  { label: '學術應用', start: 5, end: 14 },
  { label: '實用技巧', start: 15, end: 17 },
  { label: '開始使用', start: 18, end: 18 },
]

const activeChapterIndex = computed(() => {
  const page = currentSlideNo.value
  for (let i = chapters.length - 1; i >= 0; i--) {
    if (page >= chapters[i].start && page <= chapters[i].end) {
      return i
    }
  }
  return -1
})

/** Hide the nav bar on cover and section divider slides */
const navHiddenSlides = new Set([1, 2, 5, 15])
const navVisible = computed(() => !navHiddenSlides.has(currentSlideNo.value))

/** Hide bottom decorations only on cover slide */
const bottomHiddenSlides = new Set([1])
const bottomVisible = computed(() => !bottomHiddenSlides.has(currentSlideNo.value))

function navigateTo(slideNo) {
  go(slideNo)
}
</script>

<template>
  <div v-if="navVisible" class="nav-bar">
    <button
      v-for="(chapter, index) in chapters"
      :key="chapter.label"
      :class="['nav-btn', { active: index === activeChapterIndex }]"
      @click="navigateTo(chapter.start)"
    >
      {{ chapter.label }}
    </button>
  </div>

  <div v-if="bottomVisible" class="bottom-decorations">
    <div class="page-number">
      {{ $slidev.nav.currentSlideNo }} / {{ $slidev.nav.total }}
    </div>
    <div class="progress-track">
      <div
        class="progress-fill"
        :style="{ width: `${($slidev.nav.currentSlideNo / $slidev.nav.total) * 100}%` }"
      ></div>
    </div>
  </div>
</template>

<style scoped>
.nav-bar {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  display: flex;
  align-items: stretch;
  height: 18px;
  background: #fff;
  border-bottom: 1px solid #3D6869;
  font-family: 'Source Sans Pro', sans-serif;
}

.nav-btn {
  all: unset;
  box-sizing: border-box;
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 10px;
  color: #9ca3af;
  font-weight: 400;
  cursor: pointer;
  white-space: nowrap;
  transition: color 0.2s ease, background-color 0.2s ease;
  user-select: none;
}

.nav-btn:hover {
  color: #6b7280;
}

.nav-btn.active {
  color: #fff;
  background: #3D6869;
  font-weight: 600;
}

.bottom-decorations {
  pointer-events: none;
}

.page-number {
  position: absolute;
  bottom: 14px;
  right: 24px;
  font-size: 12px;
  color: #999;
  font-weight: 700;
}

.progress-track {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: #e0e0e0;
}

.progress-fill {
  height: 100%;
  background: #3D6869;
  transition: width 0.3s ease;
}
</style>
