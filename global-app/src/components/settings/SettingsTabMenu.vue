<!-- src/components/settings/SettingsTabMenu.vue -->
<template>
  <div class="stm-wrap" ref="wrapRef">
    <button class="stm-dots" :class="{ 'stm-dots--active': open }" aria-label="More settings" @click="open = !open">
      <svg width="15" height="15" viewBox="0 0 24 24" fill="currentColor">
        <circle cx="5"  cy="12" r="2.1"/>
        <circle cx="12" cy="12" r="2.1"/>
        <circle cx="19" cy="12" r="2.1"/>
      </svg>
    </button>

    <Transition name="stm-drop">
      <div v-if="open" class="stm-dropdown" @click.stop>
        <button v-for="tab in otherTabs" :key="tab.id" class="stm-item" @click="select(tab.id)">
          <span class="stm-icon">
            <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor"
              stroke-width="2" stroke-linecap="round" v-html="tab.icon"/>
          </span>
          {{ tab.label }}
        </button>
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const props = defineProps({
  tabs:      { type: Array,  required: true },
  currentId: { type: String, required: true },
})
const emit = defineEmits(['select'])

const open   = ref(false)
const wrapRef = ref(null)

const otherTabs = computed(() => props.tabs.filter(t => t.id !== props.currentId))

function select(id) {
  open.value = false
  emit('select', id)
}

function handleOutsideClick(e) {
  if (!wrapRef.value?.contains(e.target)) open.value = false
}
onMounted(()      => document.addEventListener('click', handleOutsideClick))
onBeforeUnmount(() => document.removeEventListener('click', handleOutsideClick))
</script>

<style scoped>
.stm-wrap { position: relative; flex-shrink: 0; }

.stm-dots {
  width: 30px; height: 30px; border-radius: 8px;
  display: flex; align-items: center; justify-content: center;
  background: rgba(255,255,255,.05); border: 1px solid var(--border-soft);
  color: var(--t3); cursor: pointer; transition: all 0.2s;
}
.stm-dots:hover, .stm-dots--active { background: rgba(240, 80, 37,.1); border-color: rgba(240, 80, 37,.25); color: #f05025; }

.stm-drop-enter-active, .stm-drop-leave-active { transition: opacity 0.16s ease, transform 0.16s ease; }
.stm-drop-enter-from,   .stm-drop-leave-to     { opacity: 0; transform: translateY(-6px) scale(0.97); }

.stm-dropdown {
  position: absolute; top: calc(100% + 8px); right: 0;
  min-width: 190px; z-index: 30;
  background: linear-gradient(160deg, #303030 0%, #0a0a0a 100%);
  border: 1px solid var(--border-soft);
  border-radius: 12px;
  padding: 6px;
  box-shadow: 0 12px 28px rgba(0,0,0,.35);
}
.stm-item {
  width: 100%; display: flex; align-items: center; gap: 9px;
  background: none; border: none; border-radius: 8px;
  padding: 9px 10px; font-family: inherit; font-size: 0.8rem; font-weight: 600;
  color: var(--t2); cursor: pointer; text-align: left; transition: all 0.15s;
}
.stm-item:hover { background: rgba(240, 80, 37,.1); color: #f05025; }
.stm-icon {
  width: 24px; height: 24px; border-radius: 7px; flex-shrink: 0;
  background: rgba(255,255,255,.05); border: 1px solid var(--border-soft);
  display: flex; align-items: center; justify-content: center;
}
</style>
