<!-- src/components/layout/AppLayout.vue -->
<template>
  <div class="app-layout">

    <PageLoader />
    <ToastContainer />

    <AppSidebar />

    <div class="app-layout__main">
      <AppNavbar />
      <main class="app-layout__content">
        <router-view v-slot="{ Component }">
          <transition name="page" mode="out-in">
            <component :is="Component" />
          </transition>
        </router-view>
      </main>
    </div>

  </div>
</template>

<script setup>
import AppSidebar from './AppSidebar.vue'
import AppNavbar from './AppNavbar.vue'
import PageLoader from '../ui/PageLoader.vue'
import ToastContainer from '../ui/ToastContainer.vue'
</script>

<style scoped>
.app-layout {
  min-height: 100vh;
  background: #020203;
  position: relative;
  transition: background 0.35s ease;
}

/* Clean dark background — no overlay */
.app-layout::before {
  content: '';
  position: fixed;
  inset: 0;
  background: none;
  pointer-events: none;
  z-index: 0;
}

.app-layout__main { position: relative; z-index: 1; }

[data-theme="light"] .app-layout { background: #f1f5f9; }
[data-theme="light"] .app-layout::before { display: none; }


.app-layout__main {
  margin-left: 220px;
  min-height: 100vh;
  position: relative;
  z-index: 1;
}

.app-layout__content {
  padding: 78px 24px 24px;
  max-width: 1320px;
  margin: 0 auto;
}

/* iPad Mini → iPad Pro 13" (769px–1366px) */
@media (max-width: 1366px) and (min-width: 769px) {
  .app-layout__main  { margin-left: 210px; }
  .app-layout__content { padding: 76px 18px 24px; }
}
@media (max-width: 768px) {
  .app-layout__main  { margin-left: 0; }
  .app-layout__content { padding: 84px 14px 90px; }
}
@media (max-width: 600px) {
  .app-layout__content { padding: 80px 12px 86px; }
}
@media (max-width: 430px) {
  .app-layout__content { padding: 76px 10px 84px; }
}
@media (max-width: 360px) {
  .app-layout__content { padding: 72px 8px 82px; }
}

.page-enter-active,
.page-leave-active { transition: all 0.3s ease; }
.page-enter-from   { opacity: 0; transform: translateY(12px); }
.page-leave-to     { opacity: 0; transform: translateY(-12px); }
</style>
