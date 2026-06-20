<!-- src/components/layout/AppLayout.vue -->
<template>
  <div class="app-layout">

    <!-- Fixed background layers — always rendered before cards -->
    <div class="bg-image" :style="{ backgroundImage: `url(${bgImage})` }" aria-hidden="true"></div>
    <div class="bg-overlay" aria-hidden="true"></div>

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
const bgImage = '/bg2.png'
</script>

<style scoped>
.app-layout {
  min-height: 100vh;
  background: transparent;
  position: relative;
}

/* Background image — lowest fixed layer */
.bg-image {
  position: fixed;
  inset: 0;
  z-index: 0;
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
}

/* Dark overlay — sits on top of image */
.bg-overlay {
  position: fixed;
  inset: 0;
  z-index: 1;
  background: rgba(0, 0, 0, 0.55);
  pointer-events: none;
}

.app-layout__main {
  margin-left: 220px;
  min-height: 100vh;
  position: relative;
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
