<!-- src/components/layout/AppNavbar.vue -->
<template>

  <!-- ═══════════════════════════════════
       DESKTOP NAVBAR  (hidden on mobile)
       ═══════════════════════════════════ -->
  <header class="navbar desktop-nav" :class="{ 'navbar--collapsed': !desktopOpen }">

    <!-- Left: Breadcrumb -->
    <div class="navbar__left">
      <div class="breadcrumb">
        <span class="breadcrumb__parent">{{ pageTitle }}</span>
        <span class="breadcrumb__sep">/</span>
        <span class="breadcrumb__current">{{ pageSubtitle || 'Home' }}</span>
      </div>
    </div>

    <!-- Center: Command search -->
    <div class="navbar__center" ref="searchWrapRef">
      <div class="navbar__search" :class="{ 'navbar__search--open': showResults }">
        <svg class="search-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor"
          stroke-width="2" stroke-linecap="round">
          <circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/>
        </svg>
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Search or type a command..."
          class="navbar__search-input"
          @keydown.down.prevent="moveDown"
          @keydown.up.prevent="moveUp"
          @keydown.enter.prevent="selectActive"
          @keydown.escape="closeSearch"
          @focus="searchFocused = true"
        />
        <div v-if="!searchQuery" class="search-hint">
          <kbd class="search-key">⌘</kbd>
          <kbd class="search-key">Space</kbd>
        </div>
        <button v-else class="search-clear" @click="closeSearch">
          <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
            <line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/>
          </svg>
        </button>
      </div>

      <!-- Results dropdown -->
      <Transition name="search-drop">
        <div v-if="showResults" class="search-results">
          <div v-if="filteredCommands.length === 0" class="search-no-results">
            No results for "{{ searchQuery }}"
          </div>
          <div
            v-for="(cmd, i) in filteredCommands"
            :key="cmd.path"
            class="search-result-item"
            :class="{ 'search-result-item--active': i === activeIndex }"
            @mouseenter="activeIndex = i"
            @click="navigateTo(cmd.path)"
          >
            <div class="sri-icon">
              <svg width="15" height="15" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" v-html="cmd.icon" />
            </div>
            <div class="sri-text">
              <span class="sri-name">{{ cmd.name }}</span>
              <span class="sri-desc">{{ cmd.desc }}</span>
            </div>
            <svg class="sri-arrow" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="9 18 15 12 9 6"/></svg>
          </div>
        </div>
      </Transition>
    </div>

    <!-- Right: notifications, theme, profile -->
    <div class="navbar__right">

      <!-- Notifications -->
      <div class="navbar__icon-btn" @click.stop="toggleNotifications" ref="notifBtn">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
          stroke-linecap="round" stroke-linejoin="round">
          <path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9" />
          <path d="M13.73 21a2 2 0 0 1-3.46 0" />
        </svg>
        <span v-if="unreadCount > 0" class="navbar__badge">{{ unreadCount }}</span>

        <Transition name="notif-drop">
          <div v-if="showNotifications" class="notif-dropdown" @click.stop>
            <div class="notif-dropdown__header">
              <div class="notif-header-left">
                <span class="notif-dropdown__title">Notifications</span>
                <span v-if="unreadCount > 0" class="notif-count-pill">{{ unreadCount }} new</span>
              </div>
              <span class="notif-dropdown__clear" @click.stop="clearAll">Mark all read</span>
            </div>
            <div class="notif-list">
              <div v-if="notifications.length === 0" class="notif-empty">
                <svg width="36" height="36" viewBox="0 0 24 24" fill="none" stroke="#334155"
                  stroke-width="1.5">
                  <path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9"/>
                  <path d="M13.73 21a2 2 0 0 1-3.46 0"/>
                </svg>
                <p>You're all caught up!</p>
              </div>
              <div v-for="notif in notifications" :key="notif.id"
                :class="['notif-item', `notif-item--${notif.type}`, { 'notif-item--unread': !notif.read }]"
                @click.stop="markRead(notif.id)">
                <div :class="['notif-icon', `ni--${notif.type}`]">
                  <svg v-if="notif.type === 'success'" width="13" height="13" viewBox="0 0 24 24"
                    fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
                    <polyline points="20 6 9 17 4 12"/>
                  </svg>
                  <svg v-else-if="notif.type === 'error'" width="13" height="13" viewBox="0 0 24 24"
                    fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
                    <line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/>
                  </svg>
                  <svg v-else-if="notif.type === 'warning'" width="13" height="13" viewBox="0 0 24 24"
                    fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
                    <path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/>
                    <line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/>
                  </svg>
                  <svg v-else width="13" height="13" viewBox="0 0 24 24" fill="none"
                    stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
                    <circle cx="12" cy="12" r="10"/>
                    <line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/>
                  </svg>
                </div>
                <div class="notif-item__content">
                  <p class="notif-item__title">{{ notif.title }}</p>
                  <p class="notif-item__message">{{ notif.message }}</p>
                  <span class="notif-item__time">{{ notif.time }}</span>
                </div>
                <div v-if="!notif.read" class="notif-item__dot" />
                <button class="notif-dismiss" @click.stop="dismiss(notif.id)">
                  <svg width="10" height="10" viewBox="0 0 24 24" fill="none"
                    stroke="currentColor" stroke-width="3" stroke-linecap="round">
                    <line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/>
                  </svg>
                </button>
              </div>
            </div>
            <div class="notif-footer">
              <span>{{ notifications.length }} total · {{ unreadCount }} unread</span>
              <span class="notif-footer__clear" @click.stop="clearAll">Clear all read</span>
            </div>
          </div>
        </Transition>
      </div>

      <!-- Profile chip -->
      <div class="navbar__profile-chip" ref="profileChipRef" @click.stop="toggleProfileDrop">
        <img
          src="https://api.dicebear.com/7.x/notionists/svg?seed=Daniel"
          alt="Daniel"
          class="navbar__profile-avatar"
        />
        <div class="navbar__profile-info">
          <span class="navbar__profile-name">Daniel</span>
          <span class="navbar__profile-user">@emprerorsolos...</span>
        </div>
        <button class="navbar__profile-dots" :class="{ 'navbar__profile-dots--active': profileDropOpen }" aria-label="Profile menu">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="currentColor">
            <circle cx="5"  cy="12" r="1.8"/>
            <circle cx="12" cy="12" r="1.8"/>
            <circle cx="19" cy="12" r="1.8"/>
          </svg>
        </button>

        <Transition name="profile-chip-drop">
          <div v-if="profileDropOpen" class="navbar__profile-dropdown" @click.stop>
            <div class="npd-header">
              <img
                src="https://api.dicebear.com/7.x/notionists/svg?seed=Daniel"
                alt="Daniel"
                class="npd-avatar"
              />
              <div class="npd-info">
                <span class="npd-name">Daniel</span>
                <span class="npd-email">emprerorsolos@gmail.com</span>
              </div>
            </div>

            <div class="npd-divider" />

            <button class="npd-item" @click="goTo('/settings')">
              <span class="npd-icon npd-icon--green">
                <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
                  <circle cx="12" cy="12" r="3"/>
                  <path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1-2.83 2.83l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-4 0v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83-2.83l.06-.06A1.65 1.65 0 0 0 4.68 15a1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1 0-4h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 2.83-2.83l.06.06A1.65 1.65 0 0 0 9 4.68a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 4 0v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 2.83l-.06.06A1.65 1.65 0 0 0 19.4 9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 0 4h-.09a1.65 1.65 0 0 0-1.51 1z"/>
                </svg>
              </span>
              <span class="npd-label">Account Settings</span>
              <svg class="npd-arrow" width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="9 18 15 12 9 6"/></svg>
            </button>

            <button class="npd-item" @click="goTo('/billing')">
              <span class="npd-icon npd-icon--green">
                <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
                  <line x1="12" y1="1" x2="12" y2="23"/><path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"/>
                </svg>
              </span>
              <span class="npd-label">Billing &amp; Plans</span>
              <svg class="npd-arrow" width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="9 18 15 12 9 6"/></svg>
            </button>

            <button class="npd-item" @click="goTo('/help')">
              <span class="npd-icon npd-icon--green">
                <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
                  <circle cx="12" cy="12" r="10"/><path d="M9.09 9a3 3 0 0 1 5.83 1c0 2-3 3-3 3"/><circle cx="12" cy="17" r="0.5" fill="currentColor"/>
                </svg>
              </span>
              <span class="npd-label">Help &amp; Support</span>
              <svg class="npd-arrow" width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="9 18 15 12 9 6"/></svg>
            </button>

          </div>
        </Transition>
      </div>

    </div>
  </header>

  <!-- ═══════════════════════════════════
       MOBILE NAVBAR  (hidden on desktop)
       ═══════════════════════════════════ -->
  <header class="mobile-nav">

    <!-- Hamburger -->
    <button class="mn-hamburger" @click="toggleSidebar" aria-label="Open menu">
      <span :class="['mn-bar', { 'mn-bar--open-1': sidebarOpen }]"/>
      <span :class="['mn-bar', 'mn-bar--mid', { 'mn-bar--open-2': sidebarOpen }]"/>
      <span :class="['mn-bar', { 'mn-bar--open-3': sidebarOpen }]"/>
    </button>

    <!-- Greeting + avatar -->
    <div class="mn-greeting">
      <div class="mn-avatar">
        <img
          src="https://api.dicebear.com/7.x/notionists/svg?seed=Daniel"
          width="36"
          height="36"
          alt="Avatar"
          class="mn-avatar-img"
        />
        <span class="mn-avatar-dot"/>
      </div>
      <div class="mn-greeting-text">
        <span class="mn-greet-line">{{ greeting }},</span>
        <span class="mn-name">Emperor</span>
      </div>
    </div>

    <!-- Right actions -->
    <div class="mn-actions">

      <!-- Notifications -->
      <button class="mn-icon-btn" @click.stop="toggleNotifications" ref="mobileNotifBtn">
        <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor"
          stroke-width="2" stroke-linecap="round">
          <path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9"/>
          <path d="M13.73 21a2 2 0 0 1-3.46 0"/>
        </svg>
        <span v-if="unreadCount > 0" class="mn-badge">{{ unreadCount }}</span>

        <!-- Mobile notif dropdown -->
        <Transition name="notif-drop">
          <div v-if="showNotifications" class="notif-dropdown notif-dropdown--mobile" @click.stop>
            <div class="notif-dropdown__header">
              <div class="notif-header-left">
                <span class="notif-dropdown__title">Notifications</span>
                <span v-if="unreadCount > 0" class="notif-count-pill">{{ unreadCount }} new</span>
              </div>
              <span class="notif-dropdown__clear" @click.stop="clearAll">Mark all read</span>
            </div>
            <div class="notif-list">
              <div v-if="notifications.length === 0" class="notif-empty">
                <p>You're all caught up!</p>
              </div>
              <div v-for="notif in notifications" :key="notif.id"
                :class="['notif-item', `notif-item--${notif.type}`, { 'notif-item--unread': !notif.read }]"
                @click.stop="markRead(notif.id)">
                <div :class="['notif-icon', `ni--${notif.type}`]">
                  <svg v-if="notif.type === 'success'" width="11" height="11" viewBox="0 0 24 24"
                    fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
                    <polyline points="20 6 9 17 4 12"/>
                  </svg>
                  <svg v-else width="11" height="11" viewBox="0 0 24 24" fill="none"
                    stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
                    <circle cx="12" cy="12" r="10"/>
                    <line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/>
                  </svg>
                </div>
                <div class="notif-item__content">
                  <p class="notif-item__title">{{ notif.title }}</p>
                  <p class="notif-item__message">{{ notif.message }}</p>
                </div>
              </div>
            </div>
          </div>
        </Transition>
      </button>

    </div>
  </header>

  <!-- ═══════════════════════════════════
       MOBILE BOTTOM NAV
       ═══════════════════════════════════ -->
  <nav class="mobile-bottom-nav">
    <router-link
      v-for="item in bottomNavItems" :key="item.route"
      :to="item.route"
      :class="['bn-item', { 'bn-item--active': isActiveNav(item) }]"
      @click="closeSidebar"
    >
      <svg width="21" height="21" viewBox="0 0 24 24" fill="none"
        stroke="currentColor"
        stroke-width="2" stroke-linecap="round" stroke-linejoin="round"
        v-html="item.icon" />
    </router-link>
  </nav>

</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { notifications as notifData } from '../../data/mockData.js'
import { useSidebar } from '../../composables/useSidebar.js'

const route  = useRoute()
const router = useRouter()
const { sidebarOpen, desktopOpen, toggleSidebar, closeSidebar } = useSidebar()

const bottomNavItems = [
  { route: '/',             icon: '<path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/><polyline points="9 22 9 12 15 12 15 22"/>' },
  { route: '/analytics',    icon: '<line x1="18" y1="20" x2="18" y2="10"/><line x1="12" y1="20" x2="12" y2="4"/><line x1="6" y1="20" x2="6" y2="14"/>' },
  { route: '/transactions', icon: '<polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/>' },
  { route: '/wallet',       icon: '<rect x="1" y="4" width="22" height="16" rx="2"/><line x1="1" y1="10" x2="23" y2="10"/>' },
]
const isActiveNav = (item) => route.path === item.route

/* ── Page meta ── */
const pages = {
  '/':                                    { title: 'Dashboard',            subtitle: 'Welcome back, Daniel' },
  '/customers':                           { title: 'Mailing Tools',        subtitle: 'Manage your email campaigns' },
  '/transactions':                        { title: 'Transactions',         subtitle: 'Your transaction history' },
  '/email-services/branded-bills':        { title: 'Crypto Bills Mails',   subtitle: 'Pick a provider to send a brand-styled billing email' },
  '/email-services/branded-emails':       { title: 'Crypto Mails',         subtitle: 'Pick a provider to send a brand-styled email' },
  '/email-services/composer':             { title: 'Email Composer',       subtitle: 'Write and send emails' },
  '/email-services/bank-mailer':          { title: 'Bank Mailer',          subtitle: 'Send debit & credit bank transfer notices' },
  '/analytics':                           { title: 'Websites',             subtitle: 'Monitor your web properties' },
  '/settings':                            { title: 'Settings',             subtitle: 'Manage your preferences' },
  '/tutorials':                           { title: 'Tutorials',            subtitle: 'Learn how to use every feature' },
  '/help':                                { title: 'Support',              subtitle: 'Get help and find answers' },
  '/complaint':                           { title: 'Make a Complaint',     subtitle: 'Submit and track your reports' },
  '/wallet':                              { title: 'My Wallet',            subtitle: 'Your balance and transactions' },
  '/billing':                             { title: 'Billing & Plans',      subtitle: 'Subscriptions and invoices' },
  '/tools/transactions-tracker':          { title: 'Transactions Trackers',subtitle: 'Choose a style and share a receipt link' },
  '/tools/transactions-tracker/payment':  { title: 'Payment Tracking',     subtitle: 'Create a live tracking receipt and share it instantly with a link' },
  '/tools/transactions-tracker/receipts': { title: 'Receipts Tracking',    subtitle: 'Create a live transaction receipt and share it on your own link' },
  '/tools/crypto-receipts':               { title: 'Crypto Receipts',      subtitle: 'Pick a brand and generate a branded receipt' },
  '/digital':                             { title: 'Digital',              subtitle: 'Extra tools for your business' },
  '/tools':                               { title: 'Generator',            subtitle: 'Generate branded receipts, trackers and documents' },
  '/tools/currency':                      { title: 'Currency Converter',   subtitle: 'Live exchange rates' },
  '/tools/wallet-funding':                { title: 'Wallet Funding',       subtitle: 'Crypto wallet top-ups' },
  '/tools/sms-sender':                    { title: 'SMS Sender',           subtitle: 'Send SMS messages' },
  '/tools/spoof-calling':                 { title: 'Spoof Calling',        subtitle: 'Place calls with a chosen caller ID' },
}

const currentPage    = computed(() => pages[route.path] ?? { title: 'Globalgle', subtitle: 'Banking Dashboard' })
const pageTitle      = computed(() => currentPage.value.title)
const pageSubtitle   = computed(() => currentPage.value.subtitle)

/* ── Command palette ── */
const searchQuery   = ref('')
const searchFocused = ref(false)
const activeIndex   = ref(0)
const searchWrapRef = ref(null)

const allCommands = [
  { name: 'Dashboard',          desc: 'Overview & balance',             path: '/',                                    icon: '<rect x="3" y="3" width="7" height="7" rx="1"/><rect x="14" y="3" width="7" height="7" rx="1"/><rect x="3" y="14" width="7" height="7" rx="1"/><rect x="14" y="14" width="7" height="7" rx="1"/>' },
  { name: 'Mailing',            desc: 'Customer emails',                path: '/customers',                           icon: '<path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"/><polyline points="22,6 12,13 2,6"/>' },
  { name: 'Websites / Analytics',desc: 'Website analytics',             path: '/analytics',                           icon: '<circle cx="12" cy="12" r="10"/><line x1="2" y1="12" x2="22" y2="12"/><path d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"/>' },
  { name: 'Transactions',       desc: 'View all transactions',          path: '/transactions',                        icon: '<rect x="1" y="4" width="22" height="16" rx="2"/><line x1="1" y1="10" x2="23" y2="10"/>' },
  { name: 'Digital',            desc: 'Digital tools',                  path: '/digital',                             icon: '<rect x="2" y="3" width="20" height="14" rx="2"/><line x1="8" y1="21" x2="16" y2="21"/><line x1="12" y1="17" x2="12" y2="21"/>' },
  { name: 'Generator / Tools',  desc: 'Generate receipts & documents',  path: '/tools',                               icon: '<path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"/>' },
  { name: 'Currency Converter', desc: 'Live exchange rates',            path: '/tools/currency',                      icon: '<path d="M12 2v20M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"/>' },
  { name: 'Wallet',             desc: 'My wallet & balance',            path: '/wallet',                              icon: '<rect x="2" y="5" width="20" height="14" rx="2"/><path d="M16 12h2"/><path d="M2 10h20"/>' },
  { name: 'Wallet Funding',     desc: 'Crypto wallet top-ups',          path: '/tools/wallet-funding',                icon: '<path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"/><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"/>' },
  { name: 'SMS Sender',         desc: 'Send bulk SMS messages',         path: '/tools/sms-sender',                    icon: '<path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/>' },
  { name: 'Spoof Calling',      desc: 'Place calls with chosen caller', path: '/tools/spoof-calling',                 icon: '<path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07A19.5 19.5 0 0 1 4.69 12 19.79 19.79 0 0 1 1.61 3.35 2 2 0 0 1 3.6 1h3a2 2 0 0 1 2 1.72c.127.96.361 1.903.7 2.81a2 2 0 0 1-.45 2.11L7.91 8.6a16 16 0 0 0 6 6l.94-.94a2 2 0 0 1 2.11-.45c.907.339 1.85.573 2.81.7A2 2 0 0 1 22 16.92z"/>' },
  { name: 'Crypto Receipts',    desc: 'Generate branded crypto receipt',path: '/tools/crypto-receipts',               icon: '<path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/>' },
  { name: 'Branded Emails',     desc: 'Send bank-branded emails',       path: '/email-services/branded-emails',       icon: '<path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"/><polyline points="22,6 12,13 2,6"/>' },
  { name: 'Branded Bills',      desc: 'Send branded bill receipts',     path: '/email-services/branded-bills',        icon: '<path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/>' },
  { name: 'Bank Mailer',        desc: 'Send bank notification emails',  path: '/email-services/bank-mailer',          icon: '<path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"/>' },
  { name: 'Billing & Plans',    desc: 'Manage your subscription',       path: '/billing',                             icon: '<path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/><line x1="9" y1="13" x2="15" y2="13"/><line x1="9" y1="17" x2="12" y2="17"/>' },
  { name: 'Settings',           desc: 'Account & preferences',          path: '/settings',                            icon: '<circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1-2.83 2.83l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-4 0v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83-2.83l.06-.06A1.65 1.65 0 0 0 4.68 15a1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1 0-4h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 2.83-2.83l.06.06A1.65 1.65 0 0 0 9 4.68a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 4 0v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 2.83l-.06.06A1.65 1.65 0 0 0 19.4 9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 0 4h-.09a1.65 1.65 0 0 0-1.51 1z"/>' },
  { name: 'Help & Support',     desc: 'Get help and FAQs',              path: '/help',                                icon: '<circle cx="12" cy="12" r="10"/><path d="M9.09 9a3 3 0 0 1 5.83 1c0 2-3 3-3 3"/><line x1="12" y1="17" x2="12.01" y2="17"/>' },
  { name: 'Tutorials',          desc: 'Learn how to use Globalgle',     path: '/tutorials',                           icon: '<polygon points="23 7 16 12 23 17 23 7"/><rect x="1" y="5" width="15" height="14" rx="2"/>' },
  { name: 'Complaint',          desc: 'Submit a complaint or report',   path: '/complaint',                           icon: '<path d="M4 15s1-1 4-1 5 2 8 2 4-1 4-1V3s-1 1-4 1-5-2-8-2-4 1-4 1z"/><line x1="4" y1="22" x2="4" y2="15"/>' },
]

const filteredCommands = computed(() => {
  if (!searchQuery.value.trim()) return []
  const q = searchQuery.value.toLowerCase()
  return allCommands.filter(c =>
    c.name.toLowerCase().includes(q) || c.desc.toLowerCase().includes(q)
  ).slice(0, 6)
})

const showResults = computed(() => searchFocused.value && searchQuery.value.trim().length > 0)

function navigateTo(path) {
  router.push(path)
  closeSearch()
}
function closeSearch() {
  searchQuery.value = ''
  searchFocused.value = false
  activeIndex.value = 0
}
function moveDown() {
  if (filteredCommands.value.length) activeIndex.value = (activeIndex.value + 1) % filteredCommands.value.length
}
function moveUp() {
  if (filteredCommands.value.length) activeIndex.value = (activeIndex.value - 1 + filteredCommands.value.length) % filteredCommands.value.length
}
function selectActive() {
  if (filteredCommands.value[activeIndex.value]) navigateTo(filteredCommands.value[activeIndex.value].path)
}

/* ── Shared state ── */
const showNotifications = ref(false)
const notifBtn          = ref(null)
const mobileNotifBtn    = ref(null)
const notifications     = ref(notifData)

/* ── Profile chip dropdown ── */
const profileDropOpen = ref(false)
const profileChipRef  = ref(null)
const toggleProfileDrop = () => { profileDropOpen.value = !profileDropOpen.value }
function goTo(path) { profileDropOpen.value = false; router.push(path) }
const unreadCount = computed(() => notifications.value.filter(n => !n.read).length)

/* ── Time-based greeting ── */
const greeting = computed(() => {
  const h = new Date().getHours()
  if (h < 12) return 'Good morning'
  if (h < 17) return 'Good afternoon'
  if (h < 21) return 'Good evening'
  return 'Good night'
})

/* ── Notifications ── */
const toggleNotifications = () => { showNotifications.value = !showNotifications.value }
const markRead  = (id) => { const n = notifications.value.find(n => n.id === id); if (n) n.read = true }
const dismiss   = (id) => { notifications.value = notifications.value.filter(n => n.id !== id) }
const clearAll  = () => { notifications.value = notifications.value.map(n => ({ ...n, read: true })) }

/* ── Click outside ── */
const handleOutsideClick = (e) => {
  const clickedNotifBtn   = notifBtn.value?.contains(e.target)
  const clickedMobileBtn  = mobileNotifBtn.value?.contains(e.target)
  if (!clickedNotifBtn && !clickedMobileBtn) showNotifications.value = false
  if (!profileChipRef.value?.contains(e.target)) profileDropOpen.value = false
  if (!searchWrapRef.value?.contains(e.target)) { searchFocused.value = false }
}
onMounted(()       => document.addEventListener('click', handleOutsideClick))
onBeforeUnmount(() => document.removeEventListener('click', handleOutsideClick))
</script>

<style scoped>
/* ══════════════════════════════
   DESKTOP NAVBAR
   ══════════════════════════════ */
.navbar {
  position: fixed;
  top: 0; left: 260px; right: 0;
  transition: left 0.32s cubic-bezier(0.4, 0, 0.2, 1);
  height: 64px;
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  padding: 0 22px;
  background: var(--glass);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  border-bottom: 1px solid var(--border-soft);
  z-index: 90;
  gap: 16px;
}

.navbar__left   { display: flex; align-items: center; gap: 10px; flex-shrink: 0; }
.navbar__center { display: flex; align-items: center; justify-content: center; }
.navbar__right  { display: flex; align-items: center; gap: 8px; justify-content: flex-end; }

.breadcrumb { display: flex; align-items: center; gap: 7px; }
.breadcrumb__parent  { font-size: 0.82rem; font-weight: 500; color: rgba(255,255,255,0.38); }
.breadcrumb__sep     { font-size: 0.82rem; color: rgba(255,255,255,0.22); }
.breadcrumb__current { font-size: 0.82rem; font-weight: 700; color: rgba(255,255,255,0.88); }

.navbar__search {
  display: flex;
  align-items: center;
  gap: 8px;
  position: relative;
  isolation: isolate;
  overflow: hidden;
  background: transparent;
  border: 1px solid rgba(255, 255, 255, 0.16);
  border-radius: 12px;
  padding: 9px 14px;
  width: 340px;
  box-shadow: none;
  transition: all 0.25s ease;
}
.navbar__search::before {
  content: '';
  position: absolute;
  inset: -60px;
  background: url('/bg2.png') center / cover no-repeat;
  filter: blur(60px) saturate(180%) brightness(0.35);
  z-index: -1;
  border-radius: inherit;
}
.navbar__search:focus-within {
  border-color: rgba(255, 255, 255, 0.24);
  width: 380px;
}
.navbar__search:focus-within::before {
  filter: blur(60px) saturate(200%) brightness(0.30);
}
.search-icon { width: 14px; height: 14px; color: rgba(255,255,255,0.32); flex-shrink: 0; }
.navbar__search-input {
  background: transparent; border: none; outline: none;
  font-family: 'Space Grotesk', sans-serif; font-size: 0.8rem;
  color: rgba(255,255,255,0.88); width: 100%;
}
.navbar__search-input::placeholder { color: rgba(255,255,255,0.28); }
.search-hint {
  display: flex;
  align-items: center;
  gap: 3px;
  flex-shrink: 0;
}
.search-key {
  padding: 2px 6px;
  background: rgba(255,255,255,0.07);
  border: 1px solid rgba(255,255,255,0.11);
  border-radius: 5px;
  font-size: 0.6rem;
  color: rgba(255,255,255,0.3);
  font-family: 'Space Grotesk', sans-serif;
  font-style: normal;
}
.search-clear {
  background: none; border: none; cursor: pointer; padding: 2px;
  color: rgba(255,255,255,0.4); display: flex; align-items: center;
  flex-shrink: 0; border-radius: 4px;
  transition: color 0.15s;
}
.search-clear:hover { color: rgba(255,255,255,0.8); }

/* ── Search results dropdown ── */
.navbar__center { position: relative; }

.search-results {
  position: absolute;
  top: calc(100% + 8px);
  left: 50%;
  transform: translateX(-50%);
  width: 420px;
  background: rgba(8, 8, 18, 0.95);
  border: 1px solid rgba(255,255,255,0.14);
  border-radius: 14px;
  overflow: hidden;
  z-index: 999;
  box-shadow: none;
}
.search-no-results {
  padding: 20px 16px;
  text-align: center;
  font-size: 0.8rem;
  color: rgba(255,255,255,0.35);
}
.search-result-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px 14px;
  cursor: pointer;
  transition: background 0.15s;
  border-bottom: 1px solid rgba(255,255,255,0.05);
}
.search-result-item:last-child { border-bottom: none; }
.search-result-item:hover,
.search-result-item--active { background: rgba(255,255,255,0.07); }

.sri-icon {
  width: 30px; height: 30px; flex-shrink: 0;
  display: flex; align-items: center; justify-content: center;
  background: rgba(255,255,255,0.07);
  border: 1px solid rgba(255,255,255,0.10);
  border-radius: 8px;
  color: rgba(255,255,255,0.75);
}
.sri-text { flex: 1; min-width: 0; }
.sri-name { display: block; font-size: 0.82rem; font-weight: 600; color: rgba(255,255,255,0.90); }
.sri-desc { display: block; font-size: 0.72rem; color: rgba(255,255,255,0.38); margin-top: 1px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.sri-arrow { color: rgba(255,255,255,0.25); flex-shrink: 0; }
.search-result-item--active .sri-arrow { color: rgba(255,255,255,0.6); }

/* ── Dropdown transition ── */
.search-drop-enter-active, .search-drop-leave-active { transition: opacity 0.18s ease, transform 0.18s ease; }
.search-drop-enter-from, .search-drop-leave-to { opacity: 0; transform: translateX(-50%) translateY(-6px); }

.navbar__icon-btn {
  position: relative; width: 40px; height: 40px;
  display: flex; align-items: center; justify-content: center;
  background: transparent; border: none;
  cursor: pointer; transition: color 0.2s ease; flex-shrink: 0;
}
.navbar__icon-btn svg { width: 24px; height: 24px; color: var(--t2); }
.navbar__icon-btn:hover svg { color: var(--t1); }

.navbar__badge {
  position: absolute; top: -4px; right: -4px;
  min-width: 18px; height: 18px; background: var(--red);
  border-radius: 9px; font-size: 0.6rem; font-weight: 700; color: #fff;
  display: flex; align-items: center; justify-content: center;
  border: 2px solid var(--bg); padding: 0 3px;
}

.navbar__profile-chip {
  position: relative;
  display: flex; align-items: center; gap: 9px;
  padding: 4px;
  background: transparent; border: none;
  flex-shrink: 0;
  cursor: pointer;
  transition: opacity 0.2s;
  user-select: none;
}
.navbar__profile-chip:hover { opacity: 0.8; }
.navbar__profile-avatar {
  width: 30px; height: 30px;
  border-radius: 50%;
  object-fit: cover;
  border: 2px solid var(--border);
  flex-shrink: 0;
}
.navbar__profile-info { display: flex; flex-direction: column; gap: 1px; }
.navbar__profile-name { font-size: 0.78rem; font-weight: 700; color: var(--t1); line-height: 1; }
.navbar__profile-user { font-size: 0.65rem; font-weight: 500; color: var(--t3); line-height: 1; }

.navbar__profile-dots {
  width: 24px; height: 24px;
  display: flex; align-items: center; justify-content: center;
  border-radius: 50%; border: none;
  background: transparent; color: var(--t3);
  cursor: pointer; flex-shrink: 0;
  transition: background 0.18s, color 0.18s, transform 0.18s;
}
.navbar__profile-dots:hover,
.navbar__profile-dots--active { background: rgba(255,255,255,0.1); color: var(--t1); }
.navbar__profile-dots--active { transform: rotate(90deg); }

.navbar__profile-dropdown {
  position: absolute;
  top: calc(100% + 10px); right: 0;
  min-width: 210px;
  background: rgba(4, 5, 10, 0.88);
  backdrop-filter: blur(72px) saturate(180%);
  -webkit-backdrop-filter: blur(72px) saturate(180%);
  border: 1px solid rgba(255,255,255,0.06);
  border-radius: 16px; padding: 6px;
  box-shadow: none; z-index: 200;
  transform-origin: top right;
}
.npd-header { display: flex; align-items: center; gap: 10px; padding: 10px 10px 8px; }
.npd-avatar { width: 36px; height: 36px; border-radius: 50%; object-fit: cover; border: 2px solid var(--border); flex-shrink: 0; }
.npd-info { display: flex; flex-direction: column; gap: 2px; }
.npd-name  { font-size: 0.82rem; font-weight: 700; color: var(--t1); }
.npd-email { font-size: 0.67rem; color: var(--t3); }
.npd-divider { height: 1px; background: var(--border-soft); margin: 4px 0; }
.npd-item {
  display: flex; align-items: center; gap: 10px;
  width: 100%; padding: 9px 10px;
  border-radius: 10px; border: none;
  background: transparent; color: var(--t2);
  font-family: 'Space Grotesk', sans-serif;
  font-size: 0.8rem; font-weight: 500;
  cursor: pointer; transition: background 0.15s; text-align: left;
}
.npd-item:hover { background: var(--glass-hover); }
.npd-icon { width: 28px; height: 28px; border-radius: 8px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
.npd-icon--green { background: rgba(255,255,255,0.07); color: rgba(255,255,255,0.75); }
.npd-label { flex: 1; }
.npd-arrow { color: var(--t4); flex-shrink: 0; }

.profile-chip-drop-enter-active { transition: opacity 0.2s ease, transform 0.2s cubic-bezier(0.34, 1.56, 0.64, 1); }
.profile-chip-drop-leave-active { transition: opacity 0.14s ease, transform 0.14s ease; }
.profile-chip-drop-enter-from  { opacity: 0; transform: scale(0.88) translateY(-8px); }
.profile-chip-drop-leave-to    { opacity: 0; transform: scale(0.94) translateY(-4px); }

/* ══════════════════════════════
   MOBILE NAVBAR
   ══════════════════════════════ */
.mobile-nav {
  display: none;
  position: fixed; top: 0; left: 0; right: 0; height: 64px; z-index: 90;
  background: var(--glass);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  border-bottom: 1px solid var(--border-soft);
  align-items: center; justify-content: space-between;
  padding: 0 16px; gap: 12px;
  transition: background 0.35s ease;
}

.mn-hamburger {
  width: 40px; height: 40px; background: transparent; border: none;
  border-radius: 11px; display: flex; flex-direction: column;
  align-items: center; justify-content: center; gap: 5px;
  cursor: pointer; flex-shrink: 0; padding: 0;
}
.mn-bar {
  width: 18px; height: 2px; background: rgba(255,255,255,0.90); border-radius: 2px;
  transition: all 0.28s ease; transform-origin: center;
}
.mn-bar--mid    { width: 13px; align-self: flex-start; margin-left: 11px; }
.mn-bar--open-1 { transform: rotate(45deg) translate(5px, 5px); width: 18px; }
.mn-bar--open-2 { opacity: 0; transform: scaleX(0); }
.mn-bar--open-3 { transform: rotate(-45deg) translate(5px, -5px); width: 18px; }

.mn-greeting { display: flex; align-items: center; gap: 10px; flex: 1; min-width: 0; }
.mn-avatar {
  position: relative;
  width: 36px; height: 36px; min-width: 36px; min-height: 36px;
  border-radius: 50%; flex-shrink: 0;
  overflow: hidden;
  border: 2px solid rgba(255,255,255,0.15);
  background: #000;
}
.mn-avatar-img {
  width: 36px; height: 36px;
  max-width: 36px; max-height: 36px;
  object-fit: cover; object-position: center top;
  display: block;
  filter: grayscale(1) brightness(1.2);
}
.mn-avatar-dot {
  position: absolute; bottom: 1px; right: 1px;
  width: 8px; height: 8px; background: #fff; border-radius: 50%;
  border: 2px solid var(--bg);
}
.mn-greeting-text { display: flex; flex-direction: column; gap: 1px; min-width: 0; }
.mn-greet-line { font-size: 0.68rem; color: var(--t3); font-weight: 500; white-space: nowrap; }
.mn-name { font-size: 0.88rem; font-weight: 800; color: var(--t1); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }

.mn-actions { display: flex; align-items: center; gap: 6px; flex-shrink: 0; }
.mn-icon-btn {
  position: relative; width: 38px; height: 38px;
  display: flex; align-items: center; justify-content: center;
  background: transparent; border: none;
  border-radius: 10px; cursor: pointer; color: rgba(255,255,255,0.90);
  transition: all 0.2s;
}
.mn-icon-btn:hover { color: #fff; }
.mn-badge {
  position: absolute; top: -4px; right: -4px;
  min-width: 17px; height: 17px; background: var(--red);
  border-radius: 8px; font-size: 0.58rem; font-weight: 700; color: #fff;
  display: flex; align-items: center; justify-content: center;
  border: 2px solid var(--bg); padding: 0 3px;
}

/* ══════════════════════════════
   NOTIFICATION DROPDOWN (shared)
   ══════════════════════════════ */
.notif-drop-enter-active, .notif-drop-leave-active { transition: opacity 0.18s ease, transform 0.18s ease; }
.notif-drop-enter-from,   .notif-drop-leave-to     { opacity: 0; transform: translateY(-6px) scale(0.97); }

.notif-dropdown {
  position: absolute; top: calc(100% + 12px); right: 0;
  width: 340px;
  background: rgba(8, 8, 18, 0.45);
  border: 1px solid rgba(255,255,255,0.10);
  border-radius: 18px;
  overflow: hidden;
  z-index: 200;
  isolation: isolate;
}
.notif-dropdown::before {
  content: '';
  position: absolute;
  inset: -60px;
  background: url('/bg2.png') center / cover no-repeat;
  filter: blur(60px) saturate(180%) brightness(0.28);
  z-index: -1;
  pointer-events: none;
}
.notif-dropdown--mobile {
  position: fixed;
  top: 70px; left: 12px; right: 12px;
  width: auto;
  max-height: calc(100vh - 90px);
  overflow-y: auto;
  border-radius: 16px;
}
.notif-dropdown__header {
  display: flex; justify-content: space-between; align-items: center;
  padding: 14px 16px 12px; border-bottom: 1px solid var(--border-soft);
}
.notif-header-left    { display: flex; align-items: center; gap: 8px; }
.notif-dropdown__title { font-size: 0.88rem; font-weight: 700; color: var(--t1); }
.notif-count-pill {
  font-size: 0.65rem; font-weight: 700;
  background: rgba(255,255,255,0.08); color: rgba(255,255,255,0.70);
  border: 1px solid rgba(255,255,255,0.14); border-radius: 999px; padding: 2px 8px;
}
.notif-dropdown__clear { font-size: 0.72rem; font-weight: 600; color: rgba(255,255,255,0.50); cursor: pointer; transition: color 0.2s; }
.notif-dropdown__clear:hover { color: rgba(255,255,255,0.90); }

.notif-list { max-height: 300px; overflow-y: auto; }
.notif-list::-webkit-scrollbar { width: 3px; }
.notif-list::-webkit-scrollbar-thumb { background: var(--border); border-radius: 99px; }

.notif-empty { display: flex; flex-direction: column; align-items: center; gap: 8px; padding: 28px 16px; color: var(--t3); font-size: 0.8rem; }
.notif-item {
  display: flex; align-items: flex-start; gap: 10px;
  padding: 11px 16px; border-bottom: 1px solid var(--border-soft);
  cursor: pointer; transition: background 0.15s; position: relative;
}
.notif-item:last-child  { border-bottom: none; }
.notif-item:hover       { background: var(--glass-2); }
.notif-item--unread     { background: rgba(255,255,255,0.03); }
.notif-item--unread:hover { background: rgba(255,255,255,0.06); }
.notif-item--success { border-left: 2px solid rgba(255,255,255,0.25); }
.notif-item--error   { border-left: 2px solid rgba(255,255,255,0.25); }
.notif-item--warning { border-left: 2px solid rgba(255,255,255,0.25); }
.notif-item--info    { border-left: 2px solid rgba(255,255,255,0.25); }

.notif-icon { width: 26px; height: 26px; border-radius: 8px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; margin-top: 1px; }
.ni--success { background: rgba(255,255,255,0.07); color: rgba(255,255,255,0.75); }
.ni--error   { background: rgba(255,255,255,0.07); color: rgba(255,255,255,0.75); }
.ni--warning { background: rgba(255,255,255,0.07); color: rgba(255,255,255,0.75); }
.ni--info    { background: rgba(255,255,255,0.07); color: rgba(255,255,255,0.75); }

.notif-item__content { flex: 1; display: flex; flex-direction: column; gap: 2px; min-width: 0; }
.notif-item__title   { font-size: 0.78rem; font-weight: 700; color: var(--t1); margin: 0; }
.notif-item__message { font-size: 0.73rem; color: var(--t2); margin: 0; line-height: 1.4; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
.notif-item__time    { font-size: 0.67rem; color: var(--t3); margin-top: 2px; }
.notif-item__dot     { width: 6px; height: 6px; border-radius: 50%; background: rgba(255,255,255,0.40); flex-shrink: 0; margin-top: 6px; }
.notif-dismiss {
  width: 20px; height: 20px; border-radius: 50%; border: none; background: transparent;
  color: var(--t3); cursor: pointer; display: flex; align-items: center; justify-content: center;
  opacity: 0; transition: opacity 0.15s, background 0.15s; flex-shrink: 0; margin-top: 2px;
}
.notif-item:hover .notif-dismiss { opacity: 1; }
.notif-dismiss:hover { background: rgba(248,113,113,.15); color: #f87171; }

.notif-footer {
  display: flex; justify-content: space-between; align-items: center;
  padding: 10px 16px; border-top: 1px solid var(--border-soft);
  font-size: 0.7rem; color: var(--t3);
}
.notif-footer__clear { color: var(--t2); cursor: pointer; font-weight: 600; transition: color 0.2s; }
.notif-footer__clear:hover { color: #f87171; }

/* ══════════════════════════════
   MOBILE BOTTOM NAV
   ══════════════════════════════ */
.mobile-bottom-nav {
  display: none;
  position: fixed;
  bottom: 22px; left: 50%;
  transform: translateX(-50%);
  z-index: 100;
  background: var(--glass);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  border: 1px solid var(--border-soft);
  border-radius: 30px;
  padding: 7px; gap: 6px;
  align-items: center;
}
.bn-item {
  width: 54px; height: 54px; border-radius: 20px;
  display: flex; align-items: center; justify-content: center;
  text-decoration: none; transition: all 0.22s ease;
  flex-shrink: 0; color: rgba(255, 255, 255, 0.28); position: relative;
}
.bn-item:not(.bn-item--active):hover { color: rgba(255, 255, 255, 0.55); }
.bn-item--active {
  background: rgba(255, 255, 255, 0.10);
  border: 1px solid rgba(255, 255, 255, 0.15);
  color: rgba(255, 255, 255, 0.90);
}

/* ══════════════════════════════
   RESPONSIVE: show/hide
   ══════════════════════════════ */
@media (max-width: 1366px) and (min-width: 769px) {
  .navbar { padding: 0 18px; left: 245px; }
  .navbar__search { width: 280px; }
  .navbar__search:focus-within { width: 320px; }
}

@media (min-width: 769px) {
  .navbar--collapsed { left: 0; }
}
@media (max-width: 1024px) and (min-width: 769px) {
  .navbar__search { width: 240px; }
  .navbar__search:focus-within { width: 270px; }
}
@media (max-width: 768px) {
  .desktop-nav       { display: none; }
  .mobile-nav        { display: flex; }
  .mobile-bottom-nav { display: flex; }
}
</style>