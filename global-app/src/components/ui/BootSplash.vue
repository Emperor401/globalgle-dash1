<!-- src/components/ui/BootSplash.vue -->
<template>
  <Transition name="boot-fade">
    <div v-if="visible" class="boot-splash">
      <div class="boot-splash__wrap">
        <div class="boot-splash__ring"></div>
        <img :src="logoSrc" alt="Globalgle" class="boot-splash__mark" />
      </div>
      <span class="boot-splash__name">Globalgle</span>
    </div>
  </Transition>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useBootSplash } from '../../composables/useBootSplash.js'
import { useTheme } from '../../composables/useTheme.js'
import darkLogo from '../../assets/orgimg.jpeg'
import lightLogo from '../../assets/whitelogo.png'

const { finishBooting } = useBootSplash()
const { theme } = useTheme()
const logoSrc = computed(() => theme.value === 'light' ? lightLogo : darkLogo)

const visible = ref(true)

onMounted(() => {
  setTimeout(() => {
    visible.value = false
    finishBooting()
  }, 3000)
})
</script>

<style scoped>
.boot-splash {
  position: fixed;
  inset: 0;
  z-index: 100000;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 16px;
  background: var(--bg);
}

.boot-splash__wrap {
  position: relative;
  width: 76px;
  height: 76px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.boot-splash__ring {
  position: absolute;
  inset: 0;
  border-radius: 50%;
  border: 2.5px solid var(--border-soft);
  border-top-color: var(--accent);
  animation: boot-spin 1s linear infinite;
}

.boot-splash__mark {
  position: relative;
  z-index: 1;
  width: 44px;
  height: 44px;
  border-radius: 50%;
  object-fit: cover;
  animation: boot-breathe 1.8s ease-in-out infinite;
}

.boot-splash__name {
  font-size: 0.9rem;
  font-weight: 700;
  letter-spacing: 0.02em;
  color: var(--t2);
  animation: boot-name-in 0.6s ease 0.15s both;
}

@keyframes boot-spin {
  to { transform: rotate(360deg); }
}

@keyframes boot-name-in {
  from { opacity: 0; transform: translateY(4px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes boot-breathe {
  0%, 100% { transform: scale(1); opacity: 1; }
  50%      { transform: scale(0.92); opacity: 0.8; }
}

.boot-fade-enter-active { transition: opacity 0.2s ease; }
.boot-fade-leave-active { transition: opacity 0.4s ease; }
.boot-fade-enter-from,
.boot-fade-leave-to { opacity: 0; }
</style>
