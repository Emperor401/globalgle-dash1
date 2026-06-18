<!-- src/views/AuthView.vue -->
<template>
  <div class="auth-page">

    <!-- Background orbs -->
    <div class="auth-orb auth-orb--1" />
    <div class="auth-orb auth-orb--2" />

    <div class="auth-card">

      <!-- Icon -->
      <div class="auth-icon">
        <svg width="26" height="26" viewBox="0 0 24 24" fill="none" stroke="#22c55e"
          stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <rect x="3" y="11" width="18" height="11" rx="2"/>
          <path d="M7 11V7a5 5 0 0 1 10 0v4"/>
        </svg>
      </div>

      <!-- Title -->
      <div class="auth-titles">
        <h1 class="auth-title">Site Admin</h1>
        <p class="auth-sub">Sign in to <span class="auth-brand">Globalgle</span></p>
      </div>

      <!-- Form -->
      <form class="auth-form" @submit.prevent="unlock">
        <div class="auth-input-wrap" :class="{ 'auth-input-wrap--error': error }">
          <svg class="auth-input-icon" width="15" height="15" viewBox="0 0 24 24" fill="none"
            stroke="currentColor" stroke-width="2" stroke-linecap="round">
            <rect x="3" y="11" width="18" height="11" rx="2"/>
            <path d="M7 11V7a5 5 0 0 1 10 0v4"/>
          </svg>
          <input
            v-model="code"
            :type="showPass ? 'text' : 'password'"
            class="auth-input"
            placeholder="Enter access code"
            autocomplete="current-password"
            autofocus
            @input="error = false"
          />
          <button type="button" class="auth-eye" @click="showPass = !showPass" tabindex="-1">
            <svg v-if="!showPass" width="15" height="15" viewBox="0 0 24 24" fill="none"
              stroke="currentColor" stroke-width="2" stroke-linecap="round">
              <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/>
              <circle cx="12" cy="12" r="3"/>
            </svg>
            <svg v-else width="15" height="15" viewBox="0 0 24 24" fill="none"
              stroke="currentColor" stroke-width="2" stroke-linecap="round">
              <path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94"/>
              <path d="M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19"/>
              <line x1="1" y1="1" x2="23" y2="23"/>
            </svg>
          </button>
        </div>

        <p v-if="error" class="auth-error">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor"
            stroke-width="2.5" stroke-linecap="round">
            <circle cx="12" cy="12" r="10"/>
            <line x1="12" y1="8" x2="12" y2="12"/>
            <line x1="12" y1="16" x2="12.01" y2="16"/>
          </svg>
          Incorrect access code. Try again.
        </p>

        <button type="submit" class="auth-btn" :class="{ 'auth-btn--loading': loading }">
          <span v-if="!loading">Unlock Dashboard →</span>
          <span v-else class="auth-spinner" />
        </button>
      </form>

      <p class="auth-footer">Protected by Globalgle Security</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const code     = ref('')
const error    = ref(false)
const showPass = ref(false)
const loading  = ref(false)

function unlock() {
  if (!code.value.trim()) return
  loading.value = true

  setTimeout(() => {
    if (code.value.trim() === 'globalgle') {
      localStorage.setItem('globalgle_auth', 'true')
      router.push('/')
    } else {
      error.value   = true
      loading.value = false
      code.value    = ''
    }
  }, 600)
}
</script>

<style scoped>
.auth-page {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #05060f;
  position: relative;
  overflow: hidden;
  padding: 20px;
}

/* Ambient orbs */
.auth-orb {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  pointer-events: none;
}
.auth-orb--1 {
  width: 420px; height: 420px;
  background: rgba(34,197,94,0.08);
  top: -100px; left: -100px;
}
.auth-orb--2 {
  width: 360px; height: 360px;
  background: rgba(34,197,94,0.05);
  bottom: -80px; right: -80px;
}

/* Card */
.auth-card {
  position: relative;
  z-index: 1;
  width: 100%;
  max-width: 400px;
  background: rgba(10, 12, 24, 0.85);
  backdrop-filter: blur(48px) saturate(160%);
  -webkit-backdrop-filter: blur(48px) saturate(160%);
  border: 1px solid rgba(255,255,255,0.08);
  border-radius: 24px;
  padding: 40px 36px 32px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  box-shadow: 0 32px 80px rgba(0,0,0,0.7), inset 0 1px 0 rgba(255,255,255,0.06);
}

/* Lock icon box */
.auth-icon {
  width: 64px; height: 64px;
  border-radius: 18px;
  background: rgba(34,197,94,0.12);
  border: 1px solid rgba(34,197,94,0.25);
  display: flex; align-items: center; justify-content: center;
  box-shadow: 0 8px 32px rgba(34,197,94,0.15);
}

/* Titles */
.auth-titles { text-align: center; display: flex; flex-direction: column; gap: 4px; }
.auth-title  { font-size: 1.4rem; font-weight: 800; color: #fff; margin: 0; letter-spacing: -0.02em; }
.auth-sub    { font-size: 0.85rem; color: rgba(255,255,255,0.45); margin: 0; }
.auth-brand  { color: #22c55e; font-weight: 700; }

/* Form */
.auth-form {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.auth-input-wrap {
  display: flex; align-items: center; gap: 10px;
  background: rgba(255,255,255,0.05);
  border: 1px solid rgba(255,255,255,0.1);
  border-radius: 12px;
  padding: 0 14px;
  transition: border-color 0.2s, background 0.2s;
}
.auth-input-wrap:focus-within {
  border-color: rgba(34,197,94,0.5);
  background: rgba(34,197,94,0.04);
}
.auth-input-wrap--error {
  border-color: rgba(248,113,113,0.5) !important;
  background: rgba(248,113,113,0.04) !important;
}

.auth-input-icon { color: rgba(255,255,255,0.3); flex-shrink: 0; }

.auth-input {
  flex: 1;
  background: transparent;
  border: none;
  outline: none;
  padding: 14px 0;
  font-family: 'Outfit', sans-serif;
  font-size: 0.9rem;
  color: #fff;
  letter-spacing: 0.02em;
}
.auth-input::placeholder { color: rgba(255,255,255,0.3); }

.auth-eye {
  background: none; border: none; cursor: pointer;
  color: rgba(255,255,255,0.3); display: flex; align-items: center;
  padding: 0; flex-shrink: 0; transition: color 0.2s;
}
.auth-eye:hover { color: rgba(255,255,255,0.7); }

/* Error */
.auth-error {
  display: flex; align-items: center; gap: 6px;
  font-size: 0.78rem; color: #f87171; margin: 0;
  padding: 8px 12px;
  background: rgba(248,113,113,0.08);
  border: 1px solid rgba(248,113,113,0.2);
  border-radius: 8px;
}

/* Button */
.auth-btn {
  width: 100%;
  padding: 14px;
  border-radius: 12px;
  border: none;
  background: linear-gradient(135deg, #22c55e, #16a34a);
  color: #fff;
  font-family: 'Outfit', sans-serif;
  font-size: 0.95rem;
  font-weight: 700;
  letter-spacing: 0.01em;
  cursor: pointer;
  transition: opacity 0.2s, transform 0.15s, box-shadow 0.2s;
  display: flex; align-items: center; justify-content: center;
  box-shadow: 0 4px 20px rgba(34,197,94,0.3);
  min-height: 50px;
}
.auth-btn:hover  { opacity: 0.92; transform: translateY(-1px); box-shadow: 0 8px 28px rgba(34,197,94,0.4); }
.auth-btn:active { transform: translateY(0); }
.auth-btn--loading { opacity: 0.7; pointer-events: none; }

/* Spinner */
.auth-spinner {
  width: 18px; height: 18px;
  border: 2px solid rgba(255,255,255,0.3);
  border-top-color: #fff;
  border-radius: 50%;
  animation: spin 0.7s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }

/* Footer */
.auth-footer {
  font-size: 0.72rem;
  color: rgba(255,255,255,0.2);
  margin: 0;
  letter-spacing: 0.03em;
}

/* Mobile */
@media (max-width: 430px) {
  .auth-card { padding: 32px 22px 26px; border-radius: 20px; }
  .auth-icon  { width: 56px; height: 56px; border-radius: 15px; }
  .auth-title { font-size: 1.2rem; }
}
</style>
