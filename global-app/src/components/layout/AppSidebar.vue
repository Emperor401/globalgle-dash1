



<!-- src/components/layout/AppSidebar.vue -->
<template>

  <!-- Backdrop (mobile only) -->
  <Transition name="backdrop-fade">
    <div v-if="sidebarOpen" class="sidebar-backdrop" @click="closeSidebar"/>
  </Transition>

  <aside class="sidebar" :class="{ 'sidebar--open': sidebarOpen, 'sidebar--desktop-closed': !desktopOpen }" ref="sidebarEl">

    <!-- Logo -->
    <div class="sidebar__logo">
      <div class="sidebar__logo-icon">
        <img
          :src="logoSrc"
          alt="Globalgle logo"
          class="logo-img"
        />
      </div>
      <span class="sidebar__logo-name">Globalgle</span>

      <!-- Toggle / close button -->
      <button class="vaulto-badge" @click="handleBadgeClick" aria-label="Toggle sidebar">
        <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round">
          <polyline points="11 17 6 12 11 7"/>
          <polyline points="18 17 13 12 18 7"/>
        </svg>
      </button>
    </div>

    <!-- Scrollable nav area -->
    <div class="sidebar__scroll" ref="scrollEl">

    <!-- GO MENU -->
    <div class="sidebar__group">
      <span class="sidebar__group-label">Go Menu</span>
      <nav class="sidebar__nav">
        <router-link to="/" class="nav-item" :class="{ 'nav-item--active': $route.path === '/' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M3 9.5L12 3l9 6.5V20a1 1 0 0 1-1 1H5a1 1 0 0 1-1-1V9.5z"/><polyline points="9 21 9 12 15 12 15 21"/>
            </svg>
          </div>
          <span class="nav-item__label">Dashboard</span>
        </router-link>
      </nav>
    </div>

    <!-- TOOLS -->
    <div class="sidebar__group">
      <span class="sidebar__group-label">Tools</span>
      <nav class="sidebar__nav">
        <router-link to="/customers" class="nav-item" :class="{ 'nav-item--active': $route.path === '/customers' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"/><polyline points="22,6 12,13 2,6"/>
            </svg>
          </div>
          <span class="nav-item__label">Mailing</span>
        </router-link>

        <router-link to="/analytics" class="nav-item" :class="{ 'nav-item--active': $route.path === '/analytics' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <circle cx="12" cy="12" r="10"/><line x1="2" y1="12" x2="22" y2="12"/><path d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"/>
            </svg>
          </div>
          <span class="nav-item__label">Websites</span>
        </router-link>

        <router-link to="/tools" class="nav-item" :class="{ 'nav-item--active': $route.path === '/tools' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"/>
            </svg>
          </div>
          <span class="nav-item__label">Generator</span>
          <span class="nav-item__badge">TOOLS</span>
        </router-link>

        <router-link to="/digital" class="nav-item" :class="{ 'nav-item--active': $route.path === '/digital' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="2" y="3" width="20" height="14" rx="2"/><line x1="8" y1="21" x2="16" y2="21"/><line x1="12" y1="17" x2="12" y2="21"/>
            </svg>
          </div>
          <span class="nav-item__label">Digital</span>
          <span class="nav-item__badge">TOOLS</span>
        </router-link>
      </nav>
    </div>

    <!-- SYSTEM -->
    <div class="sidebar__group">
      <span class="sidebar__group-label">System</span>
      <nav class="sidebar__nav">
        <router-link to="/wallet" class="nav-item" :class="{ 'nav-item--active': $route.path === '/wallet' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="2" y="5" width="20" height="14" rx="2"/><path d="M16 12h2"/><path d="M2 10h20"/>
            </svg>
          </div>
          <span class="nav-item__label">My Wallet</span>
          <span class="nav-item__badge">₦0</span>
        </router-link>

        <router-link to="/billing" class="nav-item" :class="{ 'nav-item--active': $route.path === '/billing' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/><line x1="9" y1="13" x2="15" y2="13"/><line x1="9" y1="17" x2="12" y2="17"/>
            </svg>
          </div>
          <span class="nav-item__label">Billing &amp; Plans</span>
        </router-link>

        <router-link to="/tutorials" class="nav-item" :class="{ 'nav-item--active': $route.path === '/tutorials' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <polygon points="23 7 16 12 23 17 23 7"/><rect x="1" y="5" width="15" height="14" rx="2"/>
            </svg>
          </div>
          <span class="nav-item__label">Tutorials</span>
        </router-link>
      </nav>
    </div>

    <!-- OTHER -->
    <div class="sidebar__group">
      <span class="sidebar__group-label">Other</span>
      <nav class="sidebar__nav">
        <router-link to="/settings" class="nav-item" :class="{ 'nav-item--active': $route.path === '/settings' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1-2.83 2.83l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-4 0v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83-2.83l.06-.06A1.65 1.65 0 0 0 4.68 15a1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1 0-4h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 2.83-2.83l.06.06A1.65 1.65 0 0 0 9 4.68a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 4 0v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 2.83l-.06.06A1.65 1.65 0 0 0 19.4 9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 0 4h-.09a1.65 1.65 0 0 0-1.51 1z"/>
            </svg>
          </div>
          <span class="nav-item__label">Settings</span>
        </router-link>

        <router-link to="/help" class="nav-item" :class="{ 'nav-item--active': $route.path === '/help' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <circle cx="12" cy="12" r="10"/><path d="M9.09 9a3 3 0 0 1 5.83 1c0 2-3 3-3 3"/><line x1="12" y1="17" x2="12.01" y2="17"/>
            </svg>
          </div>
          <span class="nav-item__label">Support</span>
        </router-link>

        <router-link to="/complaint" class="nav-item" :class="{ 'nav-item--active': $route.path === '/complaint' }" @click="closeSidebar">
          <div class="nav-item__icon">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M4 15s1-1 4-1 5 2 8 2 4-1 4-1V3s-1 1-4 1-5-2-8-2-4 1-4 1z"/><line x1="4" y1="22" x2="4" y2="15"/>
            </svg>
          </div>
          <span class="nav-item__label">Complaint</span>
        </router-link>
      </nav>
    </div>

      <!-- Upgrade CTA -->
      <div class="sidebar__upgrade">
        <div class="upgrade__header">
          <span class="upgrade__icon">
            <svg width="14" height="14" viewBox="0 0 24 24" fill="currentColor">
              <path d="M13 2 3 14h7l-1 8 10-12h-7l1-8z"/>
            </svg>
          </span>
          <p class="upgrade__title">Upgrade Pro!</p>
        </div>
        <p class="upgrade__desc">Upgrade to Pro and elevate your experience today</p>
        <div class="upgrade__actions">
          <button class="upgrade__btn" @click.stop="goUpgrade">
            <svg width="11" height="11" viewBox="0 0 24 24" fill="currentColor"><path d="M13 2 3 14h7l-1 8 10-12h-7l1-8z"/></svg>
            Upgrade
          </button>
          <a class="upgrade__learn" @click.stop="goUpgrade">Learn more</a>
        </div>
      </div>

      <!-- Logout button -->
      <button class="sidebar__logout" @click="logout">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/>
          <polyline points="16 17 21 12 16 7"/>
          <line x1="21" y1="12" x2="9" y2="12"/>
        </svg>
        Log Out
      </button>

    </div><!-- end sidebar__scroll -->

    <!-- Scrollbar-rail active indicator -->
    <div class="nav-indicator" :style="indicatorStyle"></div>

  </aside>
</template>

<script setup>
import { ref, computed, watch, nextTick, onMounted, onUnmounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import darkLogo from '../../assets/orgimg.jpeg'
import lightLogo from '../../assets/whitelogo.png'
import { useSidebar } from '../../composables/useSidebar.js'
import { useTheme } from '../../composables/useTheme.js'

const { sidebarOpen, desktopOpen, closeSidebar, toggleDesktopSidebar } = useSidebar()
const { theme } = useTheme()
const logoSrc = computed(() => theme.value === 'light' ? lightLogo : darkLogo)
const router = useRouter()
const route  = useRoute()

function handleBadgeClick() {
  if (window.innerWidth >= 769) {
    toggleDesktopSidebar()
  } else {
    closeSidebar()
  }
}

/* Reset sidebar scroll to top on every route change */
const scrollEl = ref(null)
const sidebarEl = ref(null)
const indicatorStyle = ref({ top: '0px', height: '32px', opacity: '0', transition: 'top 0.25s ease, opacity 0.2s ease' })

function updateIndicator() {
  const activeItem = document.querySelector('.nav-item--active')
  const sidebar = sidebarEl.value
  if (!activeItem || !sidebar) {
    indicatorStyle.value = { ...indicatorStyle.value, opacity: '0' }
    return
  }
  const itemRect = activeItem.getBoundingClientRect()
  const sidebarRect = sidebar.getBoundingClientRect()
  indicatorStyle.value = {
    top: `${itemRect.top - sidebarRect.top}px`,
    height: `${itemRect.height}px`,
    opacity: '1',
    transition: 'top 0.25s ease, opacity 0.2s ease',
  }
}

watch(() => route.path, () => {
  if (scrollEl.value) scrollEl.value.scrollTop = 0
  nextTick(updateIndicator)
})

onMounted(() => {
  nextTick(updateIndicator)
  scrollEl.value?.addEventListener('scroll', updateIndicator)
})

onUnmounted(() => {
  scrollEl.value?.removeEventListener('scroll', updateIndicator)
})

function goUpgrade() {
  closeSidebar()
  router.push('/billing')
}

function logout() {
  closeSidebar()
  localStorage.removeItem('globalgle_auth')
  router.push('/auth')
}



</script>

<style scoped>
/* ── Shell ── */
.sidebar {
  width: 260px;
  height: 100vh;
  height: 100dvh; /* dynamic viewport — fixes iOS Safari address-bar clipping */
  display: flex;
  flex-direction: column;
  background: var(--bg);
  border-right: 1px solid var(--border-soft);
  box-shadow: none;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 100;
  transition: border-color 0.35s ease, transform 0.32s cubic-bezier(0.4,0,0.2,1);
  overflow: visible;
}

/* ── Desktop/tablet slide toggle ── */
@media (min-width: 769px) {
  .sidebar--desktop-closed {
    transform: translateX(-100%);
  }
}

/* ── Mobile backdrop ── */
.sidebar-backdrop {
  position: fixed; inset: 0; z-index: 99;
  background: rgba(0,0,0,.55);
}
.backdrop-fade-enter-active, .backdrop-fade-leave-active { transition: opacity 0.28s ease; }
.backdrop-fade-enter-from,   .backdrop-fade-leave-to     { opacity: 0; }


/* ── Tablet — 769px–1366px (iPad Mini → iPad Pro 13") ── */
@media (min-width: 769px) and (max-width: 1366px) {
  .sidebar        { width: 245px; }
  .sidebar__group { padding: 18px 14px 4px; }
  .nav-item       { padding: 10px 11px; }
  .nav-item__label { font-size: 0.84rem; }
  .sidebar__nav   { gap: 3px; }
}

/* ── iPad landscape short viewport (height ≤ 900px, width ≥ 769px) ── */
@media (max-height: 900px) and (min-width: 769px) {
  .sidebar__logo  { padding: 12px 16px 10px; }
  .sidebar__group { padding: 10px 12px 3px; }
  .nav-item       { padding: 6px 9px; }
  .nav-item__icon { width: 28px; height: 28px; border-radius: 8px; }

  .sidebar__upgrade {
    min-height: auto;
    padding: 12px 13px;
    margin: 0 12px 10px;
  }
  .upgrade__body  { padding-right: 78px; }
  .upgrade__title { font-size: 1rem; }
  .upgrade__btn   { margin-top: 8px; padding: 5px 14px; font-size: 0.73rem; }
  .upgrade__deco-img { width: 76px; height: 76px; }

  .sidebar__logout {
    margin: 0 12px 14px;
    width: calc(100% - 24px);
    padding: 9px 0;
    font-size: 0.78rem;
  }
}

/* ── Very short viewport — hide upgrade card (height ≤ 700px) ── */
@media (max-height: 700px) and (min-width: 769px) {
  .sidebar__upgrade { display: none; }
}

@media (max-width: 768px) {
  .sidebar {
    transform: translateX(-100%);
    z-index: 100;
    width: 260px;
    background: var(--bg);
    border-right: 1px solid var(--border-soft);
  }
  .sidebar--open { transform: translateX(0); }
  .vaulto-badge  { display: none; }

  /* Compact nav on mobile */
  .sidebar__logo  { padding: 14px 16px 12px; background: transparent; }
  .sidebar__group { padding: 12px 10px 4px; background: transparent; }
  .nav-item       { padding: 9px 8px; background: transparent !important; }
  .nav-item:hover { background: transparent !important; }
  .nav-item--active { background: transparent !important; }
  .nav-item__label { font-size: 1.1rem !important; font-weight: 700 !important; }
  .nav-item__icon  { width: 28px; height: 28px; border-radius: 7px; background: transparent !important; }
  .sidebar__group-label { font-size: 0.82rem !important; letter-spacing: 0.08em; }
  .nav-item__badge { background: transparent; backdrop-filter: none; -webkit-backdrop-filter: none; }
  .sidebar__scroll { background: transparent; }

  /* Compact upgrade card */
  .sidebar__upgrade {
    min-height: auto;
    padding: 12px 13px;
    margin: 0 10px 12px;
    border-radius: 14px;
    background: transparent !important;
    backdrop-filter: none;
    -webkit-backdrop-filter: none;
  }
  .sidebar__logout { background: transparent !important; }
  .upgrade__body   { padding-right: 76px; }
  .upgrade__label  { font-size: 0.68rem; }
  .upgrade__title  { font-size: 0.95rem; }
  .upgrade__btn    { margin-top: 7px; padding: 5px 13px; font-size: 0.7rem; }
  .upgrade__deco-img { width: 72px; height: 72px; }

}

/* ── Scrollable nav wrapper ── */
.sidebar__scroll {
  flex: 1;
  overflow-y: auto;
  overflow-x: hidden;
  min-height: 0;
  padding-bottom: 4px;
  -webkit-overflow-scrolling: touch;
  position: relative;
  z-index: 1;
}

.sidebar__scroll::-webkit-scrollbar { width: 3px; }
.sidebar__scroll::-webkit-scrollbar-track { background: transparent; }
.sidebar__scroll::-webkit-scrollbar-thumb { background: var(--border); border-radius: 999px; }

/* ── Logo ── */
.sidebar__logo {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 20px 20px 18px;
  border-bottom: 1px solid var(--border-soft);
  flex-shrink: 0;
  position: relative;
  z-index: 1;
}

.sidebar__logo-icon {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  overflow: hidden;
  flex-shrink: 0;
  border: 2px solid var(--border-soft);
  background: var(--surface);
}

.logo-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 50%;
  display: block;
}


.sidebar__logo-name {
  font-weight: 800;
  font-size: 1.1rem;
  color: var(--t1);
  letter-spacing: -0.025em;
  flex: 1;
  min-width: 0;
}

/* ── Toggle/close button (gradient box) ── */
.vaulto-badge {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 26px;
  height: 26px;
  background: transparent;
  border: none;
  cursor: pointer;
  flex-shrink: 0;
  margin-right: 4px;
  outline: none;
}

/* ── Section group ── */
.sidebar__group {
  padding: 24px 4px 8px;
  flex-shrink: 0;
  position: relative;
}

.sidebar__group-label {
  display: block;
  font-size: 0.62rem;
  font-weight: 700;
  color: var(--t2);
  letter-spacing: 0.1em;
  text-transform: uppercase;
  padding: 0 8px;
  margin-bottom: 10px;
}

/* ── Nav items ── */
.sidebar__nav {
  display: flex;
  flex-direction: column;
  gap: 3px;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 7px 12px;
  border-radius: 8px;
  text-decoration: none;
  transition: all 0.22s ease;
  position: relative;
  border: 1px solid transparent;
  width: calc(100% - 28px);
  margin: 0 14px;
}

.nav-item:hover {
  background: var(--glass);
  border-color: var(--border-soft);
}

.nav-item--active {
  background: var(--glass-hover);
  border-color: var(--border);
  box-shadow: inset 0 1px 0 var(--border), 0 2px 8px rgba(0,0,0,0.15);
}

.nav-indicator {
  position: absolute;
  right: 1px;
  width: 2px;
  border-radius: 999px;
  background: var(--t1);
  pointer-events: none;
  z-index: 200;
}

.nav-item__icon {
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  color: var(--t1);
}

.nav-item__label {
  font-size: 0.845rem;
  font-weight: 600;
  color: var(--t1);
  transition: color 0.2s ease;
}

.nav-item__badge {
  font-size: 0.62rem;
  font-weight: 700;
  color: var(--t1);
  background: var(--glass-hover);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  border: 1px solid var(--border);
  padding: 2px 7px;
  border-radius: 999px;
  flex-shrink: 0;
}

/* ── Profile strip ── */
.sidebar__logout {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin: 0 14px 18px;
  padding: 11px 0;
  width: calc(100% - 28px);
  border-radius: 12px;
  border: 1.5px solid rgba(239, 68, 68, 0.3);
  background: rgba(239, 68, 68, 0.08);
  color: #ef4444;
  font-family: 'Plus Jakarta Sans', sans-serif;
  font-size: 0.82rem;
  font-weight: 700;
  cursor: pointer;
  position: relative;
  z-index: 1;
  transition: background 0.2s ease, border-color 0.2s ease, transform 0.15s ease;
}
.sidebar__logout:hover {
  background: rgba(239, 68, 68, 0.16);
  border-color: rgba(239, 68, 68, 0.55);
  transform: translateY(-1px);
}
.sidebar__logout:active { transform: translateY(0); }

/* ── Upgrade CTA ── */
.sidebar__upgrade {
  position: relative;
  z-index: 1;
  margin: 0 14px 18px;
  padding: 16px 16px 14px;
  border-radius: 14px;
  background: var(--bg);
  border: 1px solid var(--border);
  flex-shrink: 0;
}

.upgrade__header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
  padding-right: 16px;
}

.upgrade__icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 26px;
  height: 26px;
  border-radius: 8px;
  color: #fff;
  background: linear-gradient(135deg, var(--accent-grad-1) 0%, var(--accent) 55%, var(--accent-grad-3) 100%);
  box-shadow: 0 3px 10px rgba(var(--accent-r),var(--accent-g),var(--accent-b), 0.45), inset 0 1px 0 rgba(255, 255, 255, 0.25);
  flex-shrink: 0;
}

.upgrade__title {
  font-size: 0.92rem;
  font-weight: 800;
  color: var(--t1);
  margin: 0;
  line-height: 1.2;
  letter-spacing: -0.01em;
}

.upgrade__desc {
  font-size: 0.76rem;
  color: var(--t2);
  font-weight: 500;
  line-height: 1.45;
  margin: 0 0 14px;
}

.upgrade__actions {
  display: flex;
  align-items: center;
  gap: 14px;
}

.upgrade__btn {
  display: flex;
  align-items: center;
  gap: 5px;
  padding: 7px 16px;
  border-radius: 999px;
  background: linear-gradient(180deg, var(--accent-grad-1) 0%, var(--accent) 50%, var(--accent-grad-3) 100%);
  box-shadow: 0 4px 16px rgba(var(--accent-r),var(--accent-g),var(--accent-b), 0.45), inset 0 1px 0 rgba(255, 255, 255, 0.25);
  border: none;
  color: #fff;
  font-family: 'Plus Jakarta Sans', sans-serif;
  font-size: 0.76rem;
  font-weight: 700;
  cursor: pointer;
  transition: box-shadow 0.2s ease, transform 0.2s ease;
}

.upgrade__btn:hover {
  box-shadow: 0 6px 20px rgba(var(--accent-r),var(--accent-g),var(--accent-b), 0.55);
  transform: translateY(-1px);
}

.upgrade__learn {
  font-size: 0.76rem;
  font-weight: 700;
  color: var(--accent);
  cursor: pointer;
  text-decoration: none;
}

.upgrade__learn:hover {
  text-decoration: underline;
}


/* ── Safari / older iPadOS fallback — -webkit-fill-available ── */
@supports not (height: 100dvh) {
  .sidebar { height: -webkit-fill-available; }
}
</style>
