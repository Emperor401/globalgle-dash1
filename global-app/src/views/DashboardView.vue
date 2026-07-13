<!-- src/views/DashboardView.vue -->
<template>
  <div class="dash">
    <CommunityModal />

    <!-- ════════════════════════════════════
         TOOLBAR
         ════════════════════════════════════ -->
    <div class="dash-toolbar" :class="{ 'cascade-play': cascadeReady }">
      <div class="dash-toolbar__left">
        <button class="dtb-btn" @click="generateReport">
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/>
            <polyline points="14 2 14 8 20 8"/>
            <line x1="9" y1="13" x2="15" y2="13"/><line x1="9" y1="17" x2="15" y2="17"/>
          </svg>
          Generate Report
        </button>
        <button class="dtb-btn" @click="exportData">
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/>
            <polyline points="7 10 12 15 17 10"/><line x1="12" y1="15" x2="12" y2="3"/>
          </svg>
          Export
        </button>
      </div>
      <div class="dtb-search">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
          <circle cx="11" cy="11" r="7"/><line x1="21" y1="21" x2="16.65" y2="16.65"/>
        </svg>
        <input v-model="globalSearch" type="text" placeholder="Search anything..." />
      </div>
    </div>

    <!-- ════════════════════════════════════
         STAT CARDS
         ════════════════════════════════════ -->
    <div class="dash-stats">
      <div v-for="s in stats" :key="s.label" class="stat-card" :class="{ 'cascade-play': cascadeReady }">
        <span class="stat-card__lbl">{{ s.label }}</span>
        <span class="stat-card__val">{{ s.value }}</span>
        <span class="stat-card__sub">
          <svg width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="12" cy="12" r="9"/><polyline points="12 7 12 12 15.5 14"/>
          </svg>
          Vs Last month
        </span>
      </div>
    </div>

    <!-- ════════════════════════════════════
         MID ROW: SPENDING LIMITS + BALANCE
         ════════════════════════════════════ -->
    <div class="dash-mid">

      <!-- AI-Generated Spending Limits -->
      <div class="mid-card spend-card" :class="{ 'cascade-play': cascadeReady }">
        <div class="spend-card__hdr">
          <span class="spend-card__ico">
            <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M12 3l1.9 4.9L19 9.8l-4.9 1.9L12 17l-1.9-5.3L5 9.8l5.1-1.9z"/>
            </svg>
          </span>
          <span class="spend-card__title">AI-Generated Spending Limits</span>
        </div>

        <span class="spend-card__val">{{ spendingTotal }}</span>
        <span class="spend-card__label">Smart Spending Limits</span>

        <div class="spend-bar">
          <div v-for="c in spendingCategories" :key="c.label" class="spend-bar__seg"
            :style="{ width: c.pct + '%', background: c.color }"/>
        </div>

        <div class="spend-legend">
          <div v-for="c in spendingCategories" :key="c.label" class="spend-legend__item">
            <span class="spend-legend__dot" :style="{ background: c.color }"/>
            {{ c.label }} ({{ c.pct }}%)
          </div>
        </div>

        <!-- Card mock -->
        <div class="pay-card">
          <div class="pay-card__top">
            <span class="pay-card__brand">VISA</span>
            <span class="pay-card__chip">
              <svg width="22" height="16" viewBox="0 0 32 24" fill="none">
                <rect x="0.5" y="0.5" width="31" height="23" rx="4" fill="url(#chipGrad)" stroke="rgba(0,0,0,0.25)"/>
                <line x1="0" y1="8" x2="32" y2="8" stroke="rgba(0,0,0,0.2)"/>
                <line x1="0" y1="16" x2="32" y2="16" stroke="rgba(0,0,0,0.2)"/>
                <line x1="11" y1="0" x2="11" y2="24" stroke="rgba(0,0,0,0.2)"/>
                <line x1="21" y1="0" x2="21" y2="24" stroke="rgba(0,0,0,0.2)"/>
                <defs>
                  <linearGradient id="chipGrad" x1="0" y1="0" x2="32" y2="24">
                    <stop offset="0" stop-color="#f3d38a"/>
                    <stop offset="1" stop-color="#c9a24d"/>
                  </linearGradient>
                </defs>
              </svg>
            </span>
          </div>
          <span class="pay-card__number">{{ cardNumber }}</span>
          <div class="pay-card__bottom">
            <span class="pay-card__holder">{{ cardHolder }}</span>
            <span class="pay-card__expiry">{{ cardExpiry }}</span>
          </div>
        </div>
      </div>

      <!-- Available Balance -->
      <div class="mid-card balance-card" :class="{ 'cascade-play': cascadeReady }">
        <div class="balance-card__hdr">
          <div>
            <span class="balance-card__lbl">Available Balance</span>
            <span class="balance-card__val">{{ availableBalance }}</span>
          </div>
          <div class="balance-card__actions">
            <button class="bal-ghost" @click="requestFunds">
              <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.3" stroke-linecap="round" stroke-linejoin="round">
                <line x1="7" y1="17" x2="17" y2="7"/><polyline points="7 7 17 7 17 17"/>
              </svg>
              Request
            </button>
            <button class="bal-primary" @click="transferFunds">
              <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.3" stroke-linecap="round" stroke-linejoin="round">
                <line x1="5" y1="12" x2="19" y2="12"/><polyline points="12 5 19 12 12 19"/>
              </svg>
              Transfer
            </button>
            <button class="bal-more" aria-label="More options">
              <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round">
                <line x1="4" y1="7" x2="20" y2="7"/><line x1="4" y1="12" x2="20" y2="12"/><line x1="4" y1="17" x2="20" y2="17"/>
              </svg>
            </button>
          </div>
        </div>

        <div class="view-toggle">
          <button :class="['vt-btn', { 'vt-btn--on': balanceView === 'line' }]" @click="balanceView = 'line'">Line view</button>
          <button :class="['vt-btn', { 'vt-btn--on': balanceView === 'bar' }]" @click="balanceView = 'bar'">Bar view</button>
        </div>

        <div class="balance-chart">
          <div class="balance-chart__grid">
            <span v-for="lbl in yAxisLabels" :key="lbl" class="balance-chart__gridlbl">{{ lbl }}</span>
          </div>

          <!-- Bar view -->
          <div v-if="balanceView === 'bar'" class="balance-bars">
            <div v-for="b in balanceBars" :key="b.m" class="bbar-col">
              <div class="bbar-track">
                <div class="bbar-fill" :class="{ 'bbar-fill--active': b.active }" :style="{ height: (b.val / maxBalanceVal * 100) + '%' }"/>
              </div>
            </div>
          </div>

          <!-- Line view -->
          <svg v-else class="balance-line" viewBox="0 0 400 140" preserveAspectRatio="none">
            <path :d="balanceLinePath" fill="none" stroke="var(--accent)" stroke-width="2.4" stroke-linecap="round" stroke-linejoin="round"/>
            <circle v-for="(p, i) in balanceLinePoints" :key="i" :cx="p[0]" :cy="p[1]" r="3"
              :fill="balanceBars[i].active ? 'var(--accent)' : 'var(--t3)'"/>
          </svg>

          <div class="balance-chart__xlabels">
            <span v-for="b in balanceBars" :key="b.m">{{ b.m }}</span>
          </div>
        </div>

        <span class="balance-card__sub">
          <svg width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="12" cy="12" r="9"/><polyline points="12 7 12 12 15.5 14"/>
          </svg>
          Vs Last month
        </span>
      </div>
    </div>

    <!-- ════════════════════════════════════
         TRANSACTIONS TABLE
         ════════════════════════════════════ -->
    <div class="pay-table-card" :class="{ 'cascade-play': cascadeReady }">
      <div class="pay-toolbar">
        <div class="pay-toolbar__left">
          <div class="pay-search">
            <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round">
              <circle cx="11" cy="11" r="7"/><line x1="21" y1="21" x2="16.65" y2="16.65"/>
            </svg>
            <input v-model="tableSearch" type="text" placeholder="Search Transaction..." />
          </div>
          <button class="pt-filter">
            <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/>
            </svg>
            Processed Date
            <svg width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="6 9 12 15 18 9"/></svg>
          </button>
          <button class="pt-filter">
            <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <polygon points="22 3 2 3 10 12.46 10 19 14 21 14 12.46 22 3"/>
            </svg>
            More
            <svg width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="6 9 12 15 18 9"/></svg>
          </button>
        </div>
        <div class="pay-toolbar__right">
          <button class="pt-action" @click="importData">
            <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="17 8 12 3 7 8"/><line x1="12" y1="3" x2="12" y2="15"/>
            </svg>
            Import
          </button>
          <button class="pt-action" @click="exportData">
            <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" y1="15" x2="12" y2="3"/>
            </svg>
            Export
          </button>
        </div>
      </div>

      <div class="pay-table-wrap">
        <table class="pay-table">
          <thead>
            <tr>
              <th>Payment ID</th>
              <th>Total Amount</th>
              <th>Payment Period</th>
              <th>Payment Method</th>
              <th>Processed Date</th>
              <th>Status</th>
            </tr>
          </thead>
          <tbody>
            <tr v-if="filteredPayments.length === 0">
              <td colspan="6" class="pay-empty">No transactions match your search.</td>
            </tr>
            <tr v-for="p in filteredPayments" :key="p.id">
              <td class="pay-id">{{ p.id }}</td>
              <td>
                <div class="pay-amount-cell">
                  <span class="pay-amount">{{ p.amount }} <span class="pay-currency">{{ p.currency }}</span></span>
                  <div class="pay-customer">
                    <img :src="p.avatar" :alt="p.customer" class="pay-avatar" />
                    {{ p.customer }}
                  </div>
                </div>
              </td>
              <td class="pay-period">{{ p.period }}</td>
              <td><span class="pay-method">{{ p.method }}</span></td>
              <td class="pay-date">{{ p.date }}</td>
              <td>
                <span class="pay-status" :class="`pay-status--${p.status.toLowerCase()}`">
                  <svg v-if="p.status === 'Resolved'" width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round"><polyline points="20 6 9 17 4 12"/></svg>
                  <svg v-else-if="p.status === 'Failed'" width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                  <svg v-else width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round"><circle cx="12" cy="12" r="9"/><polyline points="12 7 12 12 15.5 14"/></svg>
                  {{ p.status }}
                </span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import { useRouter } from 'vue-router'
import { useToast } from '../composables/useToast.js'
import { useBootSplash } from '../composables/useBootSplash.js'
import CommunityModal from '../components/ui/CommunityModal.vue'

const router = useRouter()
const { success } = useToast()

/* ── Cascade entrance: wait for the boot splash to actually start
     revealing the page before playing the card fade-up, so the
     animation is visible instead of finishing hidden behind it ── */
const { booting } = useBootSplash()
const cascadeReady = ref(!booting.value)
watch(booting, (isBooting) => {
  if (!isBooting) cascadeReady.value = true
})

/* ── Toolbar ── */
const globalSearch = ref('')
function generateReport() { success('Report generated', 'Your dashboard report is ready to download.') }
function exportData()     { success('Export started', 'Your data export will download shortly.') }
function importData()     { success('Import ready', 'Select a file to import your transactions.') }

/* ── Stat cards ── */
const stats = [
  { label: 'Total Revenue this month', value: '$21,219.24' },
  { label: 'Total Saving',             value: '$14,376.26' },
  { label: 'Taxes to be paid',         value: '$4,667.02'  },
]

/* ── AI spending limits ── */
const spendingTotal = '$4,815.23'
const spendingCategories = [
  { label: 'Shopping',      pct: 27, color: 'var(--accent)' },
  { label: 'Subscriptions', pct: 35, color: 'var(--icon-stroke)' },
  { label: 'Dining Out',    pct: 18, color: 'var(--t2)' },
  { label: 'Other',         pct: 20, color: 'var(--t3)' },
]
const cardNumber = '3455 4562 7710 3507'
const cardHolder = 'John Carter'
const cardExpiry = '02/30'

/* ── Available balance ── */
const availableBalance = '$102,175.96'
function requestFunds()  { success('Request sent', 'Your funding request has been submitted.') }
function transferFunds() { router.push('/wallet') }

const balanceView = ref('bar')
const balanceBars = [
  { m: 'Jan', val: 320, active: false },
  { m: 'Feb', val: 260, active: false },
  { m: 'Mar', val: 410, active: false },
  { m: 'Apr', val: 543, active: true  },
  { m: 'May', val: 300, active: false },
  { m: 'Jun', val: 250, active: false },
  { m: 'Jul', val: 200, active: false },
  { m: 'Aug', val: 230, active: false },
  { m: 'Sep', val: 180, active: false },
]
const maxBalanceVal = Math.max(...balanceBars.map(b => b.val))
const yAxisLabels = computed(() => {
  const steps = 4
  const out = []
  for (let i = steps; i >= 1; i--) out.push(`$${Math.round(maxBalanceVal * (i / steps))}k`)
  return out
})

const balanceLinePoints = computed(() => {
  const stepX = 400 / (balanceBars.length - 1)
  return balanceBars.map((b, i) => {
    const x = i * stepX
    const y = 130 - (b.val / maxBalanceVal) * 120
    return [x, y]
  })
})
function smoothPath(points) {
  let d = `M${points[0][0]},${points[0][1]}`
  for (let i = 1; i < points.length; i++) {
    const a = points[i - 1], b = points[i]
    const cx = a[0] + (b[0] - a[0]) * 0.5
    d += ` C${cx},${a[1]} ${cx},${b[1]} ${b[0]},${b[1]}`
  }
  return d
}
const balanceLinePath = computed(() => smoothPath(balanceLinePoints.value))

/* ── Transactions table ── */
const payments = [
  { id: 'PAY-12345XYZ', amount: '$1,164.99', currency: 'USD', customer: 'Kathryn Murphy', avatar: 'https://api.dicebear.com/9.x/micah/png?seed=KathrynMurphy&backgroundColor=7c3aed&baseColor=f9c9b6&size=40', period: 'Mar 10 - Mar 15', method: 'Wire Transfer', date: 'Mar 13', status: 'Resolved' },
  { id: 'TXN-9876540',  amount: '$1,072.98', currency: 'USD', customer: 'Guy Hawkins',     avatar: 'https://api.dicebear.com/9.x/micah/png?seed=GuyHawkins&backgroundColor=1e3a8a&baseColor=d08b5b&size=40',     period: 'Mar 11 - Mar 12', method: 'Bank Transfer', date: 'Mar 11', status: 'Failed'   },
  { id: 'INV-56789LMN', amount: '$977.98',   currency: 'USD', customer: 'Wade Warren',     avatar: 'https://api.dicebear.com/9.x/micah/png?seed=WadeWarren&backgroundColor=92400e&baseColor=d08b5b&size=40',     period: 'Mar 4 - Mar 8',   method: 'Wire Transfer', date: 'Mar 7',  status: 'Processed' },
  { id: 'ORD-99887PQR', amount: '$535.98',   currency: 'USD', customer: 'Annette Black',   avatar: 'https://api.dicebear.com/9.x/micah/png?seed=AnnetteBlack&backgroundColor=9d174d&baseColor=f9c9b6&size=40',   period: 'Feb 1 - Feb 15',  method: 'Bank Transfer', date: 'Feb 12', status: 'Resolved' },
]
const tableSearch = ref('')
const filteredPayments = computed(() => {
  const q = tableSearch.value.trim().toLowerCase()
  if (!q) return payments
  return payments.filter(p =>
    p.id.toLowerCase().includes(q) || p.customer.toLowerCase().includes(q)
  )
})
</script>

<style scoped>
.dash { display: flex; flex-direction: column; gap: 18px; }

/* ══════════════════════════════════
   ENTRANCE — cascading fade-up
   ══════════════════════════════════ */
@keyframes dashFadeUp {
  from { opacity: 0; transform: translateY(26px) scale(0.98); }
  to   { opacity: 1; transform: translateY(0) scale(1); }
}

.dash-toolbar,
.dash-stats .stat-card,
.dash-mid .mid-card,
.pay-table-card {
  opacity: 0;
}

.dash-toolbar.cascade-play,
.dash-stats .stat-card.cascade-play,
.dash-mid .mid-card.cascade-play,
.pay-table-card.cascade-play {
  animation: dashFadeUp 0.7s cubic-bezier(0.16, 1, 0.3, 1) both;
}

.dash-toolbar.cascade-play                       { animation-delay: 0.05s; }
.dash-stats .stat-card.cascade-play:nth-child(1) { animation-delay: 0.12s; }
.dash-stats .stat-card.cascade-play:nth-child(2) { animation-delay: 0.20s; }
.dash-stats .stat-card.cascade-play:nth-child(3) { animation-delay: 0.28s; }
.dash-mid .mid-card.cascade-play:nth-child(1)    { animation-delay: 0.36s; }
.dash-mid .mid-card.cascade-play:nth-child(2)    { animation-delay: 0.44s; }
.pay-table-card.cascade-play                     { animation-delay: 0.52s; }

@media (prefers-reduced-motion: reduce) {
  .dash-toolbar,
  .dash-stats .stat-card,
  .dash-mid .mid-card,
  .pay-table-card {
    opacity: 1;
    animation: none;
  }
}

/* ══════════════════════════════════
   TOOLBAR
   ══════════════════════════════════ */
.dash-toolbar {
  display: flex; align-items: center; justify-content: space-between;
  gap: 14px; flex-wrap: wrap;
}
.dash-toolbar__left { display: flex; align-items: center; gap: 10px; }

.dtb-btn {
  display: inline-flex; align-items: center; gap: 7px;
  padding: 9px 16px; border-radius: 10px;
  background: var(--glass); border: 1px solid var(--border-soft);
  color: var(--t2); font-family: inherit; font-size: 0.8rem; font-weight: 600;
  cursor: pointer; transition: all 0.18s;
}
.dtb-btn:hover { color: var(--t1); border-color: rgba(var(--accent-r),var(--accent-g),var(--accent-b),0.3); background: rgba(var(--accent-r),var(--accent-g),var(--accent-b),0.08); }

.dtb-search {
  display: flex; align-items: center; gap: 8px;
  padding: 9px 14px; border-radius: 10px; min-width: 220px;
  background: var(--glass); border: 1px solid var(--border-soft);
  color: var(--t3);
}
.dtb-search input {
  flex: 1; background: none; border: none; outline: none;
  color: var(--t1); font-family: inherit; font-size: 0.82rem;
}
.dtb-search input::placeholder { color: var(--t3); }

/* ══════════════════════════════════
   STAT CARDS
   ══════════════════════════════════ */
.dash-stats { display: grid; grid-template-columns: repeat(3, 1fr); gap: 14px; }

.stat-card {
  background: var(--bg);
  border: 1px solid var(--t4); border-radius: 14px;
  padding: 16px 18px; display: flex; flex-direction: column; gap: 6px;
}
.stat-card__lbl { font-size: 0.78rem; font-weight: 600; color: var(--t3); }
.stat-card__val { font-size: 1.5rem; font-weight: 800; color: var(--t1); letter-spacing: -0.03em; }
.stat-card__sub {
  display: inline-flex; align-items: center; gap: 5px;
  font-size: 0.72rem; color: var(--t3); font-weight: 600; margin-top: 2px;
}

/* ══════════════════════════════════
   MID ROW
   ══════════════════════════════════ */
.dash-mid { display: grid; grid-template-columns: 0.85fr 1.15fr; gap: 14px; align-items: stretch; }

.mid-card {
  background: var(--bg);
  border: 1px solid var(--t4); border-radius: 16px;
  padding: 18px; display: flex; flex-direction: column;
}

/* ── Spending limits ── */
.spend-card__hdr { display: flex; align-items: center; gap: 8px; }
.spend-card__ico {
  width: 24px; height: 24px; border-radius: 7px; flex-shrink: 0;
  background: rgba(var(--accent-r),var(--accent-g),var(--accent-b),0.12); color: var(--accent);
  display: flex; align-items: center; justify-content: center;
}
.spend-card__title { font-size: 0.8rem; font-weight: 700; color: var(--t2); }
.spend-card__val { font-size: 1.55rem; font-weight: 800; color: var(--t1); letter-spacing: -0.03em; margin-top: 10px; }
.spend-card__label { font-size: 0.76rem; color: var(--t3); font-weight: 600; margin-top: 4px; margin-bottom: 12px; }

.spend-bar { display: flex; width: 100%; height: 8px; border-radius: 999px; overflow: hidden; background: var(--glass); }
.spend-bar__seg { height: 100%; }

.spend-legend {
  display: grid; grid-template-columns: 1fr 1fr; gap: 8px 14px;
  margin-top: 12px; margin-bottom: 16px;
}
.spend-legend__item { display: flex; align-items: center; gap: 7px; font-size: 0.74rem; color: var(--t2); font-weight: 600; }
.spend-legend__dot { width: 8px; height: 8px; border-radius: 50%; flex-shrink: 0; }

/* ── Card mock ── */
.pay-card {
  margin-top: auto;
  background: linear-gradient(135deg, #241209 0%, #050505 70%, #000 100%);
  border: 1px solid rgba(var(--accent-r),var(--accent-g),var(--accent-b),0.18);
  border-radius: 14px; padding: 16px 18px;
  display: flex; flex-direction: column; gap: 14px;
  position: relative; overflow: hidden;
}
.pay-card::after {
  content: ''; position: absolute; top: -40%; right: -20%;
  width: 220px; height: 220px; border-radius: 50%;
  background: radial-gradient(circle, rgba(var(--accent-r),var(--accent-g),var(--accent-b),0.16) 0%, transparent 70%);
  pointer-events: none;
}
.pay-card__top { display: flex; align-items: center; justify-content: space-between; position: relative; z-index: 1; }
.pay-card__brand { font-size: 1.1rem; font-weight: 800; font-style: italic; letter-spacing: 0.02em; color: #fff; }
.pay-card__chip { display: flex; }
.pay-card__number {
  font-size: 1rem; font-weight: 700; color: rgba(255,255,255,0.92);
  letter-spacing: 0.12em; font-family: 'Courier New', monospace; position: relative; z-index: 1;
}
.pay-card__bottom { display: flex; align-items: center; justify-content: space-between; position: relative; z-index: 1; }
.pay-card__holder { font-size: 0.78rem; font-weight: 700; color: rgba(255,255,255,0.85); text-transform: uppercase; letter-spacing: 0.04em; }
.pay-card__expiry { font-size: 0.78rem; font-weight: 700; color: rgba(255,255,255,0.6); }

/* ── Available balance ── */
.balance-card__hdr { display: flex; align-items: flex-start; justify-content: space-between; gap: 12px; flex-wrap: wrap; }
.balance-card__lbl { display: block; font-size: 0.8rem; font-weight: 700; color: var(--t2); margin-bottom: 8px; }
.balance-card__val { display: block; font-size: 1.7rem; font-weight: 800; color: var(--t1); letter-spacing: -0.03em; }

.balance-card__actions { display: flex; align-items: center; gap: 8px; }
.bal-ghost {
  display: inline-flex; align-items: center; gap: 6px;
  background: none; border: none; color: var(--t2);
  font-family: inherit; font-size: 0.8rem; font-weight: 600; cursor: pointer;
  padding: 8px 4px; transition: color 0.18s;
}
.bal-ghost:hover { color: var(--t1); }
.bal-primary {
  display: inline-flex; align-items: center; gap: 6px;
  background: var(--accent); border: none; border-radius: 999px;
  color: #fff; font-family: inherit; font-size: 0.8rem; font-weight: 700;
  padding: 8px 16px; cursor: pointer; transition: background 0.18s;
}
.bal-primary:hover { background: var(--accent-press); }
.bal-more {
  width: 32px; height: 32px; border-radius: 9px; flex-shrink: 0;
  background: var(--glass); border: 1px solid var(--border-soft);
  color: var(--t3); display: flex; align-items: center; justify-content: center;
  cursor: pointer; transition: all 0.18s;
}
.bal-more:hover { color: var(--t1); border-color: var(--border); }

.view-toggle {
  display: inline-flex; align-items: center; gap: 2px;
  background: var(--glass); border: 1px solid var(--border-soft);
  border-radius: 9px; padding: 3px; margin-top: 16px; width: fit-content;
}
.vt-btn {
  background: none; border: none; border-radius: 6px;
  padding: 6px 14px; font-family: inherit; font-size: 0.76rem; font-weight: 600;
  color: var(--t3); cursor: pointer; transition: all 0.18s;
}
.vt-btn--on { background: var(--glass-hover); color: var(--t1); }

.balance-chart { position: relative; margin-top: 18px; height: 150px; display: flex; flex-direction: column; }
.balance-chart__grid {
  position: absolute; inset: 0 0 22px 0;
  display: flex; flex-direction: column; justify-content: space-between;
  pointer-events: none;
}
.balance-chart__gridlbl {
  font-size: 0.62rem; color: var(--t3); font-weight: 600;
  border-top: 1px dashed var(--border-soft); padding-top: 2px;
}

.balance-bars { flex: 1; display: flex; align-items: flex-end; gap: 10px; padding-top: 4px; }
.bbar-col { flex: 1; height: 100%; display: flex; align-items: flex-end; }
.bbar-track { width: 100%; height: 100%; display: flex; align-items: flex-end; }
.bbar-fill {
  width: 100%; border-radius: 6px 6px 0 0;
  background: var(--glass-hover);
  transition: height 0.4s cubic-bezier(.4,0,.2,1);
}
.bbar-fill--active { background: var(--accent); }

.balance-line { flex: 1; width: 100%; overflow: visible; }

.balance-chart__xlabels {
  display: flex; justify-content: space-between; padding-top: 6px;
}
.balance-chart__xlabels span { font-size: 0.66rem; color: var(--t3); font-weight: 600; }

.balance-card__sub {
  display: inline-flex; align-items: center; gap: 5px;
  font-size: 0.72rem; color: var(--t3); font-weight: 600; margin-top: 12px;
}

/* ══════════════════════════════════
   TRANSACTIONS TABLE
   ══════════════════════════════════ */
.pay-table-card {
  background: var(--bg);
  border: 1px solid var(--t4); border-radius: 16px;
  padding: 16px 18px; display: flex; flex-direction: column; gap: 14px;
}

.pay-toolbar { display: flex; align-items: center; justify-content: space-between; gap: 12px; flex-wrap: wrap; }
.pay-toolbar__left, .pay-toolbar__right { display: flex; align-items: center; gap: 8px; flex-wrap: wrap; }

.pay-search {
  display: flex; align-items: center; gap: 7px;
  padding: 8px 12px; border-radius: 9px; min-width: 190px;
  background: var(--glass); border: 1px solid var(--border-soft);
  color: var(--t3);
}
.pay-search input {
  flex: 1; background: none; border: none; outline: none;
  color: var(--t1); font-family: inherit; font-size: 0.78rem;
}
.pay-search input::placeholder { color: var(--t3); }

.pt-filter, .pt-action {
  display: inline-flex; align-items: center; gap: 6px;
  padding: 8px 13px; border-radius: 9px;
  background: var(--glass); border: 1px solid var(--border-soft);
  color: var(--t2); font-family: inherit; font-size: 0.76rem; font-weight: 600;
  cursor: pointer; transition: all 0.18s;
}
.pt-filter:hover, .pt-action:hover { color: var(--t1); border-color: rgba(var(--accent-r),var(--accent-g),var(--accent-b),0.3); background: rgba(var(--accent-r),var(--accent-g),var(--accent-b),0.08); }

.pay-table-wrap { overflow-x: auto; }
.pay-table { width: 100%; border-collapse: collapse; min-width: 720px; }
.pay-table thead tr { border-bottom: 1px solid var(--border-soft); }
.pay-table th {
  padding: 10px 14px; font-size: 0.63rem; font-weight: 700;
  color: var(--t3); text-transform: uppercase; letter-spacing: 0.07em; text-align: left;
}
.pay-table td { padding: 12px 14px; vertical-align: middle; border-bottom: 1px solid var(--border-soft); }
.pay-table tr:last-child td { border-bottom: none; }

.pay-id { font-size: 0.78rem; font-weight: 700; color: var(--t1); }

.pay-amount-cell { display: flex; flex-direction: column; gap: 5px; }
.pay-amount { font-size: 0.85rem; font-weight: 700; color: var(--t1); }
.pay-currency { font-size: 0.68rem; color: var(--t3); font-weight: 600; }
.pay-customer { display: flex; align-items: center; gap: 6px; font-size: 0.72rem; color: var(--t3); font-weight: 600; }
.pay-avatar { width: 18px; height: 18px; border-radius: 50%; flex-shrink: 0; object-fit: cover; }

.pay-period, .pay-date { font-size: 0.78rem; color: var(--t2); font-weight: 600; white-space: nowrap; }

.pay-method {
  display: inline-flex; padding: 4px 11px; border-radius: 999px;
  background: var(--glass); border: 1px solid var(--border-soft);
  font-size: 0.7rem; font-weight: 600; color: var(--t2); white-space: nowrap;
}

.pay-status {
  display: inline-flex; align-items: center; gap: 5px;
  padding: 4px 11px; border-radius: 999px;
  font-size: 0.7rem; font-weight: 700; white-space: nowrap;
}
.pay-status--resolved  { color: #4ade80; background: rgba(74,222,128,0.12);  border: 1px solid rgba(74,222,128,0.25); }
.pay-status--failed    { color: #f87171; background: rgba(248,113,113,0.12); border: 1px solid rgba(248,113,113,0.25); }
.pay-status--processed { color: #60a5fa; background: rgba(96,165,250,0.12);  border: 1px solid rgba(96,165,250,0.25); }

.pay-empty { text-align: center; padding: 32px !important; color: var(--t3); font-size: 0.82rem; }

/* ══════════════════════════════════
   RESPONSIVE
   ══════════════════════════════════ */
@media (max-width: 1024px) {
  .dash-stats { grid-template-columns: 1fr 1fr; }
  .dash-mid   { grid-template-columns: 1fr; }
}
@media (max-width: 640px) {
  .dash-toolbar { flex-direction: column; align-items: stretch; }
  .balance-card__hdr { flex-direction: column; }

  /* Stat cards become a swipeable slider on mobile */
  .dash-stats {
    display: flex;
    grid-template-columns: unset;
    gap: 12px;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    -webkit-overflow-scrolling: touch;
    padding-bottom: 4px;
    scrollbar-width: none;
  }
  .dash-stats::-webkit-scrollbar { display: none; }

  .dash-stats .stat-card {
    flex: 0 0 82%;
    scroll-snap-align: start;
  }
}
</style>
