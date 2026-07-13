<!-- src/components/ui/BootSplash.vue -->
<template>
  <Transition name="boot-fade">
    <div v-if="visible" class="boot-splash">
      <div class="boot-splash__mark">
        <span
          v-for="(ch, i) in letters"
          :key="i"
          class="boot-splash__letter"
          :style="{ animationDelay: `${i * 0.035}s` }"
        >{{ ch }}</span>
      </div>
      <div class="boot-splash__pct">{{ pct }}%</div>
      <div class="boot-splash__bar">
        <div class="boot-splash__bar-fill" :style="{ width: pct + '%' }" />
      </div>
    </div>
  </Transition>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useBootSplash } from '../../composables/useBootSplash.js'

const { finishBooting } = useBootSplash()

const visible = ref(true)
const pct = ref(0)
const letters = 'Globalgle'.split('')

onMounted(() => {
  const duration = 2600
  const start = performance.now()

  function tick(now) {
    const linear = Math.min((now - start) / duration, 1)
    const eased = 1 - Math.pow(1 - linear, 2)
    pct.value = Math.round(eased * 100)
    if (linear < 1) {
      requestAnimationFrame(tick)
    } else {
      setTimeout(() => {
        visible.value = false
        finishBooting()
      }, 350)
    }
  }
  requestAnimationFrame(tick)
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

.boot-splash__mark {
  font-size: 2rem;
  font-weight: 800;
  letter-spacing: -0.02em;
  display: flex;
}

.boot-splash__letter {
  display: inline-block;
  background: linear-gradient(135deg, var(--accent) 0%, var(--accent-hover) 100%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  animation: boot-letter-in 0.5s ease both;
}

@keyframes boot-letter-in {
  from { opacity: 0; transform: translateY(8px); }
  to   { opacity: 1; transform: translateY(0); }
}

.boot-splash__pct {
  font-size: 0.8rem;
  font-weight: 700;
  color: var(--t3);
  letter-spacing: 0.04em;
  font-variant-numeric: tabular-nums;
}

.boot-splash__bar {
  width: 140px;
  height: 3px;
  border-radius: 999px;
  background: var(--border-soft);
  overflow: hidden;
}

.boot-splash__bar-fill {
  height: 100%;
  border-radius: 999px;
  background: linear-gradient(90deg, var(--accent) 0%, var(--accent-hover) 100%);
  transition: width 0.05s linear;
}

.boot-fade-enter-active { transition: opacity 0.2s ease; }
.boot-fade-leave-active { transition: opacity 0.4s ease; }
.boot-fade-enter-from,
.boot-fade-leave-to { opacity: 0; }
</style>
