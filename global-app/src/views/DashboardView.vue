<!-- src/views/DashboardView.vue -->
<template>
  <div class="dashboard">
    <CommunityModal />

    <!-- Page header -->
    <div class="dash-header">
      <div class="dash-header__left">
        <h1 class="dash-header__title">Dashboard</h1>
        <p class="dash-header__sub">At-a-glance view of your account, subscription, and recent activity.</p>
      </div>
    </div>

    <div class="bento">

      <!-- ╔══ Balance Card (span 2) ══╗ -->
      <div class="b-card b-balance">

        <!-- Greeting -->

        <!-- Balance row -->
        <div class="bal-top-row">
          <div class="bal-left">
            <span class="bal-label" @click="router.push('/wallet')" style="cursor:pointer">Available Balance
              <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><path d="M9 18l6-6-6-6"/></svg>
            </span>
            <div class="bal-fig">
              <span class="bal-sign">$</span>
              <span class="bal-int">260,375</span><span class="bal-dec">.03</span>
            </div>
          </div>
          <button class="add-funds-btn" @click="router.push('/wallet')">Fund Wallet</button>
        </div>

        <!-- Plan actions -->
        <div class="bal-plan-row">
          <div class="plan-badge">
            <span class="plan-badge__dot"></span>
            Current: Starter
          </div>
          <button class="upgrade-btn" @click="router.push('/billing')">Upgrade plan</button>
        </div>


      </div>

      <!-- ╔══ Quick Actions (span 2) ══╗ -->
      <div class="b-card b-quick-actions">
        <span class="section-title">Quick actions</span>
        <div class="qa-grid">
          <button class="qa-tile" @click="router.push('/customers')">
            <span class="qa-tile__icon qa-tile__icon--blue">
              <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"/>
                <polyline points="22,6 12,13 2,6"/>
              </svg>
            </span>
            <span class="qa-tile__label">Email</span>
          </button>
          <button class="qa-tile" @click="router.push('/email-services/branded-bills')">
            <span class="qa-tile__icon qa-tile__icon--amber">
              <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/>
              </svg>
            </span>
            <span class="qa-tile__label">Bills</span>
          </button>
          <button class="qa-tile" @click="router.push('/analytics')">
            <span class="qa-tile__icon qa-tile__icon--purple">
              <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <circle cx="12" cy="12" r="10"/>
                <line x1="2" y1="12" x2="22" y2="12"/>
                <path d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"/>
              </svg>
            </span>
            <span class="qa-tile__label">Websites</span>
          </button>
          <button class="qa-tile" @click="router.push('/tools')">
            <span class="qa-tile__icon qa-tile__icon--green">
              <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"/>
              </svg>
            </span>
            <span class="qa-tile__label">Tools</span>
          </button>
        </div>
      </div>

      <!-- ╔══ Wallet Overview (span 2) ══╗ -->
      <div class="b-card b-recent-txn b-wallet-overview">
        <!-- Header -->
        <div class="wo-head">
          <span class="wo-title">Your wallet</span>
          <div class="wo-filter-wrap" @click.stop>
            <div class="wo-filter" @click="filterOpen = !filterOpen">
              <span>{{ selectedPeriod }}</span>
              <svg width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"
                :style="{ transform: filterOpen ? 'rotate(180deg)' : 'rotate(0deg)', transition: 'transform 0.2s' }">
                <polyline points="6 9 12 15 18 9"/>
              </svg>
            </div>
            <Transition name="dd-pop">
              <div v-if="filterOpen" class="wo-dropdown">
                <button
                  v-for="p in periods" :key="p"
                  :class="['wo-dd-item', { 'wo-dd-item--active': p === selectedPeriod }]"
                  @click="selectPeriod(p)">
                  {{ p }}
                  <svg v-if="p === selectedPeriod" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="#22c55e" stroke-width="2.5" stroke-linecap="round"><polyline points="20 6 9 17 4 12"/></svg>
                </button>
              </div>
            </Transition>
          </div>
        </div>

        <!-- Growth + Sparkline -->
        <div class="wo-chart-wrap">
          <div class="wo-growth">
            <span class="wo-percent">{{ ap.percent }}</span>
            <span class="wo-grow-sub">{{ ap.sub }}</span>
          </div>
          <div class="wo-chart">
            <div class="wo-peak-label" :style="{ left: (ap.peakX / 300 * 100).toFixed(1) + '%' }">Highest</div>
            <svg viewBox="0 0 300 85" preserveAspectRatio="none" class="wo-sparkline">
              <defs>
                <linearGradient id="woGrad" x1="0" y1="0" x2="0" y2="1">
                  <stop offset="0%" stop-color="#22c55e" stop-opacity="0.35"/>
                  <stop offset="100%" stop-color="#22c55e" stop-opacity="0"/>
                </linearGradient>
              </defs>
              <path :d="sparkPath" fill="url(#woGrad)"/>
              <polyline :points="ap.spark" fill="none" stroke="#22c55e" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"/>
              <line :x1="ap.peakX" :y1="ap.peakY" :x2="ap.peakX" y2="85" stroke="#22c55e" stroke-width="1" stroke-dasharray="4,3" opacity="0.5"/>
              <circle :cx="ap.peakX" :cy="ap.peakY" r="4" fill="#22c55e"/>
            </svg>
          </div>
        </div>

        <!-- Bottom mini-cards -->
        <div class="wo-stats">
          <div class="wo-stat">
            <div class="wo-stat__head">
              <span class="wo-stat__label">Wallet</span>
              <button class="wo-stat__btn" @click="router.push('/wallet')">+</button>
            </div>
            <span class="wo-stat__badge">{{ ap.badge }}</span>
            <span class="wo-stat__val">{{ ap.saving }}</span>
          </div>
          <div class="wo-stat">
            <div class="wo-stat__head">
              <span class="wo-stat__label">Monthly Spend</span>
              <button class="wo-stat__btn" @click="router.push('/transactions')">→</button>
            </div>
            <span class="wo-stat__badge wo-stat__badge--spend">{{ ap.spendBadge }}</span>
            <span class="wo-stat__val">{{ ap.spend }}</span>
          </div>
        </div>
      </div>

      <!-- ╔══ Recent Payments (span 2) ══╗ -->
      <div class="b-card b-recent-pay">
        <div class="section-head">
          <span class="section-title">Recent transactions</span>
          <a class="see-all" @click="router.push('/transactions')">See all →</a>
        </div>

        <table class="pay-table">
          <thead>
            <tr>
              <th>Date</th>
              <th>Wallet</th>
              <th>Transaction</th>
              <th>Status</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="p in recentPayments" :key="p.id">
              <td>{{ p.date }}</td>
              <td>
                <div class="pay-method">
                  <div class="pay-method-icon">
                    <svg width="11" height="11" viewBox="0 0 24 24" fill="none"
                      stroke="#22c55e" stroke-width="2" stroke-linecap="round">
                      <rect x="2" y="5" width="20" height="14" rx="2"/>
                      <path d="M2 10h20"/>
                    </svg>
                  </div>
                  <span>{{ p.wallet }} <span class="pay-cur">{{ p.currency }}</span></span>
                </div>
              </td>
              <td class="pay-amount">{{ fmtAmount(p.amount, p.currency) }}</td>
              <td><span class="pay-status" :class="`pay-status--${p.status}`">{{ p.status }}</span></td>
            </tr>
          </tbody>
        </table>
      </div>


    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'
import CommunityModal from '../components/ui/CommunityModal.vue'

const router = useRouter()

/* ── Period filter ── */
const periods       = ['Days', 'Weeks', 'Months', 'Years']
const selectedPeriod = ref('Months')
const filterOpen    = ref(false)

const periodData = {
  Days:   { percent: '+2%',  sub: 'Grow since yesterday ↗',  saving: '$43',    badge: '▲ 0.8%',  spend: '$18',     spendBadge: '▼ 1.2%', spark: '0,70 20,65 40,72 60,60 80,68 100,55 120,62 145,40 165,58 185,65 205,52 230,68 260,55 300,62', peakX: 145, peakY: 40 },
  Weeks:  { percent: '+8%',  sub: 'Grow since last week ↗',  saving: '$312',   badge: '▲ 3.2%',  spend: '$124',    spendBadge: '▼ 0.8%', spark: '0,72 20,62 40,68 60,50 80,58 100,38 120,45 145,14 165,40 185,57 205,46 230,62 260,52 300,60',  peakX: 145, peakY: 14 },
  Months: { percent: '+24%', sub: 'Grow since last month ↗', saving: '$1,269', badge: '▲ 5.21%', spend: '$430',    spendBadge: '▼ 2.1%', spark: '0,68 20,60 40,66 60,48 80,56 100,35 120,42 145,8 165,38 185,55 205,44 230,60 260,50 300,58',   peakX: 145, peakY: 8  },
  Years:  { percent: '+68%', sub: 'Grow since last year ↗',  saving: '$8,420', badge: '▲ 12.4%', spend: '$5,200',  spendBadge: '▼ 8.4%', spark: '0,75 20,68 40,60 60,45 80,35 100,22 120,18 145,5 165,20 185,35 205,28 230,42 260,32 300,38',   peakX: 145, peakY: 5  },
}

const ap = computed(() => periodData[selectedPeriod.value])

const sparkPath = computed(() => {
  const pts = ap.value.spark.split(' ')
  const d = pts.map((pt, i) => {
    const [x, y] = pt.split(',')
    return i === 0 ? `M${x},${y}` : `L${x},${y}`
  }).join(' ')
  return `${d} L300,85 L0,85 Z`
})

function selectPeriod(p) { selectedPeriod.value = p; filterOpen.value = false }
function onDocClick() { filterOpen.value = false }
onMounted(()   => document.addEventListener('click', onDocClick))
onUnmounted(() => document.removeEventListener('click', onDocClick))

const recentPayments = [
  { id: 1, date: 'Jun 7, 2026', wallet: 'Main Wallet', currency: 'NGN', amount: 8,     status: 'pending'   },
  { id: 2, date: 'Jun 7, 2026', wallet: 'Main Wallet', currency: 'NGN', amount: 50000, status: 'pending'   },
  { id: 3, date: 'Jun 7, 2026', wallet: 'Main Wallet', currency: 'NGN', amount: 6000,  status: 'pending'   },
]

function fmtAmount(amount, currency = 'NGN') {
  return new Intl.NumberFormat('en-NG', {
    style: 'currency', currency,
    minimumFractionDigits: 2, maximumFractionDigits: 2,
  }).format(amount)
}
</script>

<style scoped>
/* ── Fade-up entry animation ── */
@keyframes fadeUp {
  from {
    opacity: 0;
    transform: translateY(28px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.dash-header        { animation: fadeUp 0.55s cubic-bezier(0.4, 0, 0.2, 1) 0.05s both; }
.b-balance          { animation: fadeUp 0.55s cubic-bezier(0.4, 0, 0.2, 1) 0.15s both; }
.b-recent-txn       { animation: fadeUp 0.55s cubic-bezier(0.4, 0, 0.2, 1) 0.25s both; }
.b-recent-pay       { animation: fadeUp 0.55s cubic-bezier(0.4, 0, 0.2, 1) 0.45s both; }

.dashboard { display: flex; flex-direction: column; gap: 18px; }

/* ══ PAGE HEADER ══ */
.dash-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  padding-bottom: 4px;
}

.dash-header__left { display: flex; flex-direction: column; gap: 4px; }

.dash-header__title {
  font-size: 1.6rem;
  font-weight: 800;
  color: var(--t1);
  margin: 0;
  letter-spacing: -0.02em;
}

.dash-header__sub {
  font-size: 0.82rem;
  color: var(--t2); font-weight: 500;
  margin: 0;
}

.plan-badge {
  display: flex;
  align-items: center;
  gap: 7px;
  padding: 7px 14px;
  border-radius: 999px;
  background: rgba(34,197,94,.12);
  border: 1px solid rgba(34,197,94,.25);
  color: #22c55e;
  font-size: 0.78rem;
  font-weight: 600;
}

.plan-badge__dot {
  width: 6px; height: 6px;
  border-radius: 50%;
  background: #22c55e;
  animation: pulse-dot 2s infinite;
}

.upgrade-btn {
  padding: 7px 18px;
  border-radius: 999px;
  border: 1px solid var(--border);
  background: var(--glass);
  backdrop-filter: blur(20px) saturate(180%);
 
  color: var(--t1);
  font-family: 'Outfit', sans-serif;
  font-size: 0.78rem;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.2s;
}

.upgrade-btn:hover {
  border-color: #22c55e;
  color: #22c55e;
  background: rgba(34,197,94,.08);
}

/* ══ Bento grid ══ */
.bento {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
}

/* Card base */
.b-card {
  background: rgba(255, 255, 255, 0.055);
  backdrop-filter: blur(28px) saturate(200%);
  -webkit-backdrop-filter: blur(28px) saturate(200%);
  border: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.35), inset 0 1px 0 rgba(255, 255, 255, 0.08);
  border-radius: 18px;
  padding: 16px 18px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  overflow: hidden;
  transition: border-color 0.25s ease, box-shadow 0.25s ease;
}

.b-card:hover {
  border-color: rgba(255, 255, 255, 0.18);
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.4), inset 0 1px 0 rgba(255, 255, 255, 0.12);
}

/* Column spans */
.b-balance  { grid-column: span 2; }
.b-recent-txn { grid-column: span 2; }
.b-recent-pay { grid-column: span 2; }

/* ══ BALANCE CARD ══ */

.bal-top-row {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 12px;
}

.bal-left { display: flex; flex-direction: column; gap: 22px; }

.bal-label {
  display: flex;
  align-items: center;
  gap: 3px;
  font-size: 0.8rem;
  font-weight: 600;
  color: var(--t2);
  cursor: pointer;
}
.bal-label svg { color: var(--t3); }

.bal-fig {
  display: flex;
  align-items: baseline;
  gap: 2px;
  line-height: 1;
}
.bal-sign {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--t2);
  align-self: flex-start;
  margin-top: 5px;
}
.bal-int {
  font-size: 2.8rem;
  font-weight: 800;
  color: var(--t1);
  letter-spacing: -0.04em;
}
.bal-dec {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--t2);
  letter-spacing: -0.02em;
}

/* Add funds button */
.add-funds-btn {
  padding: 10px 20px;
  border-radius: 999px;
  background: var(--accent);
  color: #fff;
  border: none;
  font-family: 'Outfit', sans-serif;
  font-size: 0.85rem;
  font-weight: 700;
  cursor: pointer;
  white-space: nowrap;
  flex-shrink: 0;
  transition: opacity 0.2s, transform 0.15s;
}
.add-funds-btn:hover { opacity: 0.88; transform: translateY(-1px); }


/* Action buttons row */
.bal-plan-row {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-top: 26px;
  flex-wrap: wrap;
}

.bal-actions {
  display: flex;
  align-items: center;
  gap: 24px;
  margin-top: 6px;
  flex-wrap: wrap;
}

.ba-tile {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  padding: 0;
  background: none;
  border: none;
  cursor: pointer;
  transition: transform 0.15s ease, opacity 0.15s ease;
  flex-shrink: 0;
  font-family: 'Outfit', sans-serif;
}
.ba-tile:hover { transform: translateY(-2px); opacity: 0.85; }
.ba-tile:active { transform: translateY(0) scale(0.97); }

.ba-tile__icon {
  width: 38px;
  height: 38px;
  border-radius: 11px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}
.ba-tile__icon--green  { background: rgba(34, 197, 94, 0.15);  color: #22c55e; }
.ba-tile__icon--amber  { background: rgba(245, 158, 11, 0.15); color: #f59e0b; }
.ba-tile__icon--blue   { background: rgba(96, 165, 250, 0.15); color: #60a5fa; }
.ba-tile__icon--purple { background: rgba(167, 139, 250, 0.15);color: #a78bfa; }
.ba-tile__icon--teal   { background: rgba(20, 184, 166, 0.15); color: #14b8a6; }

.ba-tile__label {
  font-size: 0.7rem;
  font-weight: 600;
  color: var(--t2);
  white-space: nowrap;
}

.b-quick-actions { grid-column: span 2; }

.qa-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
  margin-top: 4px;
}

.qa-tile {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 10px;
  padding: 18px 10px;
  border-radius: 14px;
  background: var(--glass-2);
  border: 1px solid var(--border-soft);
  cursor: pointer;
  transition: background 0.2s ease, transform 0.2s ease, border-color 0.2s ease;
}

.qa-tile:hover {
  background: var(--glass-hover);
  border-color: var(--border);
  transform: translateY(-2px);
}

.qa-tile:active { transform: translateY(0) scale(0.97); }

.qa-tile__icon {
  width: 44px; height: 44px;
  border-radius: 12px;
  display: flex; align-items: center; justify-content: center;
}

.qa-tile__icon--blue,
.qa-tile__icon--amber,
.qa-tile__icon--purple,
.qa-tile__icon--green  { background: rgba(34, 197, 94, 0.15); color: #22c55e; }

.qa-tile__label {
  font-size: 0.72rem;
  font-weight: 600;
  color: var(--t2);
  white-space: nowrap;
}

/* ══ SECTION HEADER ══ */
.section-head {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 4px;
}
.section-title {
  font-size: 0.95rem;
  font-weight: 700;
  color: var(--t1);
}
.see-all {
  font-size: 0.78rem;
  font-weight: 600;
  color: #22c55e;
  cursor: pointer;
  transition: opacity 0.2s;
}
.see-all:hover { opacity: 0.75; }

/* ══ EMPTY STATE ══ */
/* ── Wallet Overview ── */
.b-wallet-overview { justify-content: space-between; }

.wo-head {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.wo-title {
  font-size: 0.92rem;
  font-weight: 700;
  color: var(--t1);
}
.wo-filter-wrap { position: relative; }

.wo-filter {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 0.7rem;
  font-weight: 600;
  color: var(--t3);
  background: var(--glass-2);
  border: 1px solid var(--border-soft);
  padding: 4px 12px;
  border-radius: 999px;
  cursor: pointer;
  transition: background 0.2s;
  user-select: none;
}
.wo-filter:hover { background: var(--glass-hover); }

.wo-dropdown {
  position: absolute;
  top: calc(100% + 8px);
  right: 0;
  min-width: 130px;
  background: var(--dropdown-bg);
  border: 1px solid var(--dropdown-border);
  border-radius: 12px;
  padding: 6px;
  z-index: 100;
  display: flex;
  flex-direction: column;
  gap: 2px;
  backdrop-filter: blur(20px) saturate(180%);
}
.wo-dd-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;
  padding: 8px 12px;
  border-radius: 8px;
  border: none;
  background: none;
  color: var(--t2);
  font-family: 'Outfit', sans-serif;
  font-size: 0.78rem;
  font-weight: 500;
  cursor: pointer;
  transition: background 0.15s;
  text-align: left;
  width: 100%;
}
.wo-dd-item:hover       { background: var(--glass-hover); color: var(--t1); }
.wo-dd-item--active     { color: #22c55e; font-weight: 700; }

/* Dropdown pop animation */
.dd-pop-enter-active { transition: opacity 0.15s ease, transform 0.15s cubic-bezier(0.34,1.56,0.64,1); }
.dd-pop-leave-active { transition: opacity 0.1s ease, transform 0.1s ease; }
.dd-pop-enter-from,
.dd-pop-leave-to     { opacity: 0; transform: translateY(-6px) scale(0.97); }

.wo-chart-wrap {
  display: flex;
  flex-direction: column;
  gap: 6px;
  flex: 1;
}
.wo-growth {
  display: flex;
  flex-direction: column;
  gap: 2px;
}
.wo-percent {
  font-size: 2.2rem;
  font-weight: 800;
  color: var(--t1);
  line-height: 1;
}
.wo-grow-sub {
  font-size: 0.72rem;
  font-weight: 500;
  color: #22c55e;
}
.wo-chart {
  position: relative;
  width: 100%;
}
.wo-peak-label {
  position: absolute;
  top: -10px;
  transform: translateX(-50%);
  font-size: 0.6rem;
  font-weight: 700;
  background: #22c55e;
  color: #000;
  padding: 2px 8px;
  border-radius: 999px;
  white-space: nowrap;
  z-index: 1;
}
.wo-sparkline {
  width: 100%;
  height: 80px;
  display: block;
}

.wo-stats {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
}
.wo-stat {
  background: var(--glass-2);
  border: 1px solid var(--border-soft);
  border-radius: 12px;
  padding: 12px 14px;
  display: flex;
  flex-direction: column;
  gap: 5px;
}
.wo-stat__head {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.wo-stat__label {
  font-size: 0.72rem;
  font-weight: 600;
  color: var(--t3);
}
.wo-stat__btn {
  width: 24px; height: 24px;
  border-radius: 7px;
  background: #22c55e;
  border: none;
  color: #000;
  font-size: 1.1rem;
  font-weight: 800;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  line-height: 1;
  transition: opacity 0.2s;
}
.wo-stat__btn:hover { opacity: 0.8; }
.wo-stat__badge {
  align-self: flex-start;
  font-size: 0.62rem;
  font-weight: 700;
  color: #22c55e;
  background: rgba(34, 197, 94, 0.12);
  padding: 2px 8px;
  border-radius: 999px;
}
.wo-stat__badge--spend {
  color: #f87171;
  background: rgba(248, 113, 113, 0.12);
}
.wo-stat__sub-lbl {
  font-size: 0.62rem;
  color: var(--t3);
  font-weight: 500;
}
.wo-stat__val {
  font-size: 1.55rem;
  font-weight: 800;
  color: var(--t1);
  line-height: 1.1;
}
.wo-stat__target {
  font-size: 0.9rem;
  font-weight: 600;
  color: var(--t3);
}

/* ══ PAYMENTS TABLE ══ */
.pay-table {
  width: 100%; border-collapse: collapse; margin-top: 4px;
}
.pay-table th {
  font-size: 0.68rem; font-weight: 700; text-transform: uppercase;
  letter-spacing: 0.07em; color: var(--t3);
  padding: 0 12px 10px; text-align: left;
  border-bottom: 1px solid var(--border-soft);
}
.pay-table td {
  font-size: 0.82rem; color: var(--t2);
  padding: 12px; border-bottom: 1px solid var(--border-soft);
}
.pay-table tr:last-child td { border-bottom: none; }
.pay-table tr:hover td      { background: rgba(255,255,255,.03); }

.pay-method      { display: flex; align-items: center; gap: 8px; }
.pay-method-icon {
  width: 24px; height: 24px; border-radius: 7px;
  background: rgba(34,197,94,.1); border: 1px solid rgba(34,197,94,.2);
  display: flex; align-items: center; justify-content: center; flex-shrink: 0;
}
.pay-cur    { font-size: 0.65rem; color: var(--t4); font-weight: 600; }
.pay-amount { font-weight: 700; color: var(--t1); letter-spacing: -0.02em; }

.pay-status {
  display: inline-flex; align-items: center;
  padding: 3px 10px; border-radius: 999px;
  font-size: 0.7rem; font-weight: 700;
}
.pay-status--pending   { background: rgba(34,197,94,.12);   color: #22c55e; border: 1px solid rgba(34,197,94,.28);   }
.pay-status--completed { background: rgba(34,197,94,.12);   color: #22c55e; border: 1px solid rgba(34,197,94,.25);   }
.pay-status--failed    { background: rgba(248,113,113,.12); color: #f87171; border: 1px solid rgba(248,113,113,.25); }


/* ══════════════════════════════════════
   RESPONSIVE
   ══════════════════════════════════════ */

/* ── Tablet landscape (769px – 1024px) ── */
@media (max-width: 1024px) and (min-width: 769px) {
  .dashboard    { gap: 18px; }
  .bento        { gap: 14px; grid-template-columns: repeat(4, 1fr); }
  .b-card       { padding: 14px 16px; gap: 10px; }
  .b-balance    { grid-column: span 2; }
  .b-quick-actions { grid-column: span 2; }
  .b-recent-txn { grid-column: span 2; }
  .b-recent-pay { grid-column: span 2; }
  .bal-int      { font-size: 2.4rem; }
}

/* ── Tablet portrait (601px – 768px) ── */
@media (max-width: 768px) and (min-width: 601px) {
  .dashboard { gap: 16px; }
  .bento     { grid-template-columns: 1fr 1fr; gap: 14px; }

  .b-balance,
  .b-quick-actions { grid-column: span 2; }
  .b-recent-txn,
  .b-recent-pay    { grid-column: span 1; }

  .b-card { padding: 14px 14px; border-radius: 16px; gap: 10px; }

  .dash-header__title { font-size: 1.35rem; }
  .bal-int  { font-size: 2.2rem; }
  .bal-sign,
  .bal-dec  { font-size: 1.2rem; }

  .qa-grid  { gap: 10px; }
  .qa-tile  { padding: 14px 8px; }

  .pay-table th,
  .pay-table td { padding: 9px 7px; font-size: 0.76rem; }
  .pay-table th { font-size: 0.62rem; }
  .pay-status   { padding: 3px 9px; font-size: 0.66rem; }
}

/* ── Mobile (≤ 600px) ── */
@media (max-width: 600px) {
  .dashboard { gap: 14px; }
  .bento     { grid-template-columns: 1fr; gap: 12px; }

  .b-balance,
  .b-quick-actions,
  .b-recent-txn,
  .b-recent-pay { grid-column: span 1; }

  .b-card { padding: 12px 12px; border-radius: 16px; gap: 10px; }

  /* Header */
  .dash-header       { flex-direction: column; align-items: flex-start; gap: 6px; }
  .dash-header__title { font-size: 1.25rem; }
  .dash-header__sub   { font-size: 0.75rem; }

  /* Balance — keep side by side, shrink font to fit */
  .bal-top-row {
    flex-direction: row;
    align-items: flex-end;
    justify-content: space-between;
    gap: 10px;
  }
  .bal-int  { font-size: 1.65rem; }
  .bal-sign { font-size: 0.95rem; }
  .bal-dec  { font-size: 0.95rem; }
  .add-funds-btn {
    padding: 9px 16px;
    font-size: 0.78rem;
    white-space: nowrap;
    flex-shrink: 0;
  }

  /* Plan row */
  .bal-plan-row { gap: 8px; flex-wrap: wrap; }
  .plan-badge   { font-size: 0.7rem; padding: 5px 11px; }
  .upgrade-btn  { font-size: 0.7rem; padding: 5px 14px; }

  /* Quick actions */
  .section-title { font-size: 0.88rem; }
  .see-all       { font-size: 0.72rem; }
  .qa-grid       { gap: 10px; }
  .qa-tile       { padding: 14px 6px; gap: 8px; border-radius: 12px; }
  .qa-tile__icon { width: 40px; height: 40px; border-radius: 10px; }
  .qa-tile__label { font-size: 0.68rem; }

  /* Wallet overview */
  .wo-percent  { font-size: 1.9rem; }
  .wo-stat__val { font-size: 1.35rem; }

  /* Table */
  .b-recent-pay { overflow: hidden; }
  .pay-table    { table-layout: fixed; }
  .pay-table th,
  .pay-table td { padding: 8px 5px; font-size: 0.71rem; }
  .pay-table th { font-size: 0.58rem; padding-bottom: 8px; }
  .pay-method-icon { width: 20px; height: 20px; border-radius: 6px; }
  .pay-method   { gap: 5px; }
  .pay-cur      { display: none; }
  .pay-amount   { font-size: 0.71rem; }
  .pay-status   { padding: 2px 7px; font-size: 0.62rem; }
}

/* ── Small phones (≤ 430px) ── */
@media (max-width: 430px) {
  .b-card   { padding: 11px 11px; }
  .bal-int  { font-size: 1.45rem; }
  .bal-sign,
  .bal-dec  { font-size: 0.85rem; }
  .add-funds-btn { padding: 8px 13px; font-size: 0.73rem; }
  .wo-percent { font-size: 1.7rem; }
  .wo-stat__val { font-size: 1.2rem; }

  /* Hide Wallet column on narrow table */
  .pay-table th:nth-child(2),
  .pay-table td:nth-child(2) { display: none; }
  .pay-table th,
  .pay-table td { padding: 7px 4px; font-size: 0.68rem; }
}

/* ── Tiny phones (≤ 360px) ── */
@media (max-width: 360px) {
  .b-card   { padding: 10px 10px; border-radius: 14px; }
  .bal-int  { font-size: 1.28rem; }
  .bal-sign,
  .bal-dec  { font-size: 0.78rem; }
  .add-funds-btn { padding: 7px 11px; font-size: 0.68rem; }
  .qa-tile  { padding: 12px 4px; }
  .qa-tile__icon { width: 34px; height: 34px; }
  .qa-tile__label { font-size: 0.6rem; }
  .section-title  { font-size: 0.82rem; }
  .pay-table th,
  .pay-table td   { padding: 6px 3px; font-size: 0.63rem; }
  .pay-status     { padding: 2px 6px; font-size: 0.58rem; }
}

/* ── Light mode surface fixes ── */
[data-theme="light"] .pay-table tr:hover td { background: var(--glass-2); }

[data-theme="light"] .b-card {
  background: rgba(255, 255, 255, 0.80);
  border: 1px solid rgba(0, 0, 0, 0.11);
}
[data-theme="light"] .b-card:hover {
  border-color: rgba(0, 0, 0, 0.18);
}
[data-theme="light"] .qa-tile {
  background: rgba(255, 255, 255, 0.80);
  border-color: rgba(0, 0, 0, 0.11);
}
[data-theme="light"] .qa-tile:hover {
  border-color: rgba(0, 0, 0, 0.18);
}
[data-theme="light"] .wo-stat {
  background: rgba(255, 255, 255, 0.80);
  border-color: rgba(0, 0, 0, 0.11);
}
</style>
