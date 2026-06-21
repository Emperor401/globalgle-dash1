<!-- src/views/DashboardView.vue -->
<template>
  <div class="dash">
    <CommunityModal />

    <!-- ════════════════════════════════════
         TOTAL BALANCE HERO
         ════════════════════════════════════ -->
    <div class="hero">
      <p class="hero__lbl">Total Balance</p>
      <div class="hero__row">
        <h1 class="hero__amt">$260,375<span class="hero__dec">.03</span></h1>
        <span class="hero__badge">
          <svg width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round"><polyline points="18 15 12 9 6 15"/></svg>
          +24%
        </span>
      </div>
      <div class="hero__split">
        <span class="hero__split-item">
          <svg width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="#22c55e" stroke-width="2.5" stroke-linecap="round"><circle cx="12" cy="12" r="4"/></svg>
          NGN: ₦260,375
          <svg width="9" height="9" viewBox="0 0 24 24" fill="none" stroke="#22c55e" stroke-width="3" stroke-linecap="round"><polyline points="18 15 12 9 6 15"/></svg>
        </span>
        <span class="hero__split-item">
          <svg width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="#f87171" stroke-width="2.5" stroke-linecap="round"><circle cx="12" cy="12" r="4"/></svg>
          USD: $0.00
          <svg width="9" height="9" viewBox="0 0 24 24" fill="none" stroke="#f87171" stroke-width="3" stroke-linecap="round"><polyline points="6 9 12 15 18 9"/></svg>
        </span>
      </div>
      <div class="hero__actions">
        <button class="ha ha--green" @click="router.push('/wallet')">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
            <line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/>
          </svg>
          Add Funds
        </button>
        <button class="ha" @click="router.push('/transactions')">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
            <line x1="5" y1="12" x2="19" y2="12"/><polyline points="12 5 19 12 12 19"/>
          </svg>
          Transfer
        </button>
        <button class="ha" @click="router.push('/digital')">
          <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
            <polyline points="23 6 13.5 15.5 8.5 10.5 1 18"/><polyline points="17 6 23 6 23 12"/>
          </svg>
          Digital
        </button>
      </div>

    </div>

    <!-- ════════════════════════════════════
         CURRENCY CARDS (separate row)
         ════════════════════════════════════ -->
    <div class="cur-row">
      <div v-for="c in currencies" :key="c.code" class="cur" @click="router.push(c.route)" role="button" tabindex="0" @keydown.enter="router.push(c.route)">
        <div class="cur__texture"></div>
        <div class="cur__top">
          <div class="cur__ico-wrap">
            <span class="cur__ico">{{ c.sym }}</span>
          </div>
          <span class="cur__code">{{ c.code }}</span>
        </div>
        <span class="cur__amt">{{ c.amt }}</span>
        <div class="cur__btm">
          <svg class="cur__spark" viewBox="0 0 80 28" preserveAspectRatio="none">
            <polyline :points="c.spark" fill="none" :stroke="c.up ? '#22c55e' : '#f87171'"
              stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
          <span class="cur__chg" :class="c.up ? 'cur__chg--up' : 'cur__chg--dn'">
            <svg width="8" height="8" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round">
              <polyline v-if="c.up" points="18 15 12 9 6 15"/>
              <polyline v-else points="6 9 12 15 18 9"/>
            </svg>
            {{ c.chg }}
          </span>
        </div>
        <span class="cur__sub">from last month</span>
      </div>

      <button class="cur__add" @click="router.push('/tools/currency')">
        <div class="cur__add-inner">
          <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="rgba(255,255,255,0.35)" stroke-width="1.5" stroke-linecap="round">
            <line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/>
          </svg>
          <span>Add another currency or<br>btc to your account.</span>
        </div>
      </button>
    </div>

    <!-- ════════════════════════════════════
         CHARTS
         ════════════════════════════════════ -->
    <div class="chart-row">

      <!-- Wallet Spendings (main line chart) -->
      <div class="card chart-main">
        <div class="card__head">
          <div>
            <p class="card__lbl">Wallet Spendings</p>
            <span class="card__val">₦260,375</span>
          </div>
          <div class="period-tabs">
            <button :class="['ptab', { 'ptab--on': selectedPeriod === 'All' }]" @click="selectPeriod('All')">All</button>

            <!-- Custom dropdown -->
            <div class="custom-dd" ref="ddRef">
              <button :class="['ptab', 'ptab--custom', { 'ptab--on': showDd }]" @click="showDd = !showDd">
                {{ customLabel }}
                <svg class="dd-caret" :class="{ 'dd-caret--open': showDd }" width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="6 9 12 15 18 9"/></svg>
              </button>
              <transition name="dd">
                <div v-if="showDd" class="dd-menu">
                  <button v-for="opt in ddOptions" :key="opt.value"
                    :class="['dd-item', { 'dd-item--on': selectedPeriod === opt.value }]"
                    @click="pickDd(opt)">
                    <svg v-if="selectedPeriod === opt.value" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="20 6 9 17 4 12"/></svg>
                    <span v-else class="dd-spacer"/>
                    {{ opt.label }}
                  </button>
                </div>
              </transition>
            </div>
          </div>
        </div>
        <div class="chart-body">
          <svg viewBox="0 0 400 140" preserveAspectRatio="none" class="chart-svg" style="height:160px">
            <!-- Red line (spendings) — no fill -->
            <path :d="line2Path" fill="none" stroke="rgba(220,38,38,0.85)" stroke-width="2"
              stroke-linecap="round" stroke-linejoin="round"/>
            <!-- White/main line (balance) — no fill -->
            <path :d="linePath" fill="none" stroke="rgba(255,255,255,0.75)" stroke-width="2.2"
              stroke-linecap="round" stroke-linejoin="round"/>
            <!-- Tooltip dot (orange) on red line intersection -->
            <circle cx="220" cy="95" r="6" fill="rgba(251,146,60,0.25)"/>
            <circle cx="220" cy="95" r="3.5" fill="#fb923c"/>
          </svg>
          <!-- Tooltip callout -->
          <div class="chart-tooltip">
            <span class="ct-date">Jun 12, 2026</span>
            <span class="ct-val">Count: ₦242,940</span>
          </div>
          <div class="chart-xlabels">
            <span v-for="l in xlabels" :key="l">{{ l }}</span>
          </div>
          <!-- Numbered pagination like Vaulto -->
          <div class="chart-dots">
            <button v-for="i in 18" :key="i"
              :class="['chart-dot', { 'chart-dot--on': i === activeDot }]"
              @click="activeDot = i">{{ i }}</button>
          </div>
        </div>
      </div>

      <!-- Wallet Spendings (day bar chart) -->
      <div class="card chart-side">
        <div class="card__head">
          <div>
            <p class="card__lbl">Wallet Spendings</p>
            <span class="card__val">₦213</span>
          </div>
          <button class="see-all" @click="router.push('/wallet')">See All</button>
        </div>
        <div class="bars-wrap">
          <div v-for="b in daybars" :key="b.d" class="bar-col">
            <div class="bar-track">
              <!-- full bar -->
              <div class="bar-fill" :style="{ height: b.pct + '%' }"></div>
              <!-- red alert block at top of bar -->
              <div v-if="b.alert" class="bar-alert" :style="{ bottom: (b.pct - b.alertPct) + '%', height: b.alertPct + '%' }"></div>
            </div>
            <span class="bar-lbl" :class="{ 'bar-lbl--on': b.active }">{{ b.d }}</span>
          </div>
        </div>
      </div>
    </div>

    <!-- ════════════════════════════════════
         BOTTOM ROW
         ════════════════════════════════════ -->
    <div class="bot-row">

      <!-- Recent Activity -->
      <div class="card bot-activity">
        <div class="bot-head">
          <span class="bot-title">Recent Activity</span>
          <a class="bot-link" @click="router.push('/transactions')">See All</a>
        </div>
        <div class="act-list">

          <!-- Item 1 -->
          <div class="act-item">
            <div class="act-icon">
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                <polygon points="12,2 19,7 19,13 12,22 5,13 5,7" stroke="rgba(255,255,255,0.65)" stroke-width="1.5" fill="none" stroke-linejoin="round"/>
                <polygon points="12,2 19,7 12,11 5,7" stroke="rgba(255,255,255,0.35)" stroke-width="1" fill="rgba(255,255,255,0.08)" stroke-linejoin="round"/>
              </svg>
            </div>
            <div class="act-info">
              <span class="act-name">NGN Wallet</span>
              <span class="act-date">Received · Today</span>
            </div>
            <span class="act-amt act-amt--pos">+₦8.00</span>
          </div>

          <!-- Item 2 -->
          <div class="act-item">
            <div class="act-icon">
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="rgba(255,255,255,0.65)" stroke-width="2" stroke-linecap="round">
                <line x1="7" y1="17" x2="17" y2="7"/>
                <polyline points="7 7 17 7 17 17"/>
              </svg>
            </div>
            <div class="act-info">
              <span class="act-name">Transfer Out</span>
              <span class="act-date">Sent · Thu</span>
            </div>
            <span class="act-amt act-amt--neg">-₦50,000.00</span>
          </div>

          <!-- Item 3 -->
          <div class="act-item">
            <div class="act-icon">
              <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="rgba(255,255,255,0.65)" stroke-width="1.8" stroke-linecap="round">
                <circle cx="12" cy="12" r="3"/>
                <path d="M12 2v3M12 19v3M4.22 4.22l2.12 2.12M17.66 17.66l2.12 2.12M2 12h3M19 12h3M4.22 19.78l2.12-2.12M17.66 6.34l2.12-2.12"/>
              </svg>
            </div>
            <div class="act-info">
              <span class="act-name">Revenue</span>
              <span class="act-date">Credit · Mon</span>
            </div>
            <span class="act-amt act-amt--pos">+₦6,000.00</span>
          </div>

          <!-- Item 4 -->
          <div class="act-item">
            <div class="act-icon">
              <span class="act-num">27</span>
            </div>
            <div class="act-info">
              <span class="act-name">Globalgle Pay</span>
              <span class="act-date">Credit · Fri</span>
            </div>
            <span class="act-amt act-amt--pos">+₦378.00</span>
          </div>

        </div>
      </div>

      <!-- Total Assets (Sankey flow) -->
      <div class="card bot-sankey">
        <div class="bot-head">
          <div>
            <p class="stat-lbl">Total Assets</p>
            <span class="sankey-val">₦260,375.03</span>
          </div>
        </div>
        <div class="sankey-wrap">
          <!-- Left nodes -->
          <div class="sankey-col sankey-col--left">
            <div class="snode snode--sm">
              <span class="snode-sym">₦</span><span class="snode-lbl">NGN</span>
              <span class="snode-amt">260,375</span>
            </div>
            <div class="snode snode--sm">
              <span class="snode-sym">$</span><span class="snode-lbl">USD</span>
              <span class="snode-amt">0.00</span>
            </div>
            <div class="snode snode--sm">
              <span class="snode-sym">€</span><span class="snode-lbl">EUR</span>
              <span class="snode-amt">0.00</span>
            </div>
          </div>
          <!-- SVG flows -->
          <svg class="sankey-svg" viewBox="0 0 120 100" preserveAspectRatio="xMidYMid meet">
            <path d="M0,18 C40,18 80,48 120,48" fill="none" stroke="rgba(255,255,255,0.15)" stroke-width="8"/>
            <path d="M0,50 C40,50 80,48 120,48" fill="none" stroke="rgba(255,255,255,0.1)"  stroke-width="5"/>
            <path d="M0,80 C40,80 80,48 120,48" fill="none" stroke="rgba(255,255,255,0.07)" stroke-width="4"/>
            <path d="M120,48 C80,30 40,18 0,18"  fill="none" stroke="rgba(220,38,38,0.5)"   stroke-width="6" opacity="0.7"/>
          </svg>
          <!-- Center node -->
          <div class="sankey-center">
            <div class="snode snode--accent">
              <span class="snode-sym">₦</span>
              <span class="snode-lbl">NGN</span>
              <span class="snode-amt2">260K</span>
            </div>
          </div>
          <!-- Right SVG flows -->
          <svg class="sankey-svg" viewBox="0 0 120 100" preserveAspectRatio="xMidYMid meet">
            <path d="M0,48 C40,48 80,22 120,22" fill="none" stroke="rgba(255,255,255,0.15)" stroke-width="8"/>
            <path d="M0,48 C40,48 80,50 120,50" fill="none" stroke="rgba(255,255,255,0.1)"  stroke-width="5"/>
            <path d="M0,48 C40,48 80,78 120,78" fill="none" stroke="rgba(255,255,255,0.07)" stroke-width="4"/>
          </svg>
          <!-- Right nodes -->
          <div class="sankey-col sankey-col--right">
            <div class="snode snode--sm">
              <span class="snode-sym">$</span><span class="snode-lbl">USD</span>
              <span class="snode-amt">0.00</span>
            </div>
            <div class="snode snode--sm">
              <span class="snode-sym">€</span><span class="snode-lbl">EUR</span>
              <span class="snode-amt">0.00</span>
            </div>
            <div class="snode snode--sm">
              <span class="snode-sym">₿</span><span class="snode-lbl">BTC</span>
              <span class="snode-amt">0.00</span>
            </div>
          </div>
        </div>
      </div>

      <!-- Net Cashflow + dot heatmap -->
      <div class="card bot-cashflow">
        <div class="bot-head">
          <div>
            <p class="stat-lbl">Net Cashflow</p>
            <span class="stat-val stat-val--neg">-₦430</span>
          </div>
          <span class="cf-badge">↓ 0%</span>
        </div>
        <div class="dot-grid">
          <div v-for="(o,i) in dots" :key="i" class="dot" :style="{ opacity: o }"/>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import CommunityModal from '../components/ui/CommunityModal.vue'

const router = useRouter()

const selectedPeriod = ref('All')
const activeDot = ref(9)
function selectPeriod(p) { selectedPeriod.value = p }

/* ── Custom dropdown ── */
const ddRef = ref(null)
const showDd = ref(false)
const ddOptions = [
  { label: 'Today',        value: 'Days'   },
  { label: 'Last 7 Days',  value: 'Weeks'  },
  { label: 'Last 30 Days', value: 'Months' },
  { label: 'Last Year',    value: 'Years'  },
  { label: 'Custom Range', value: 'Custom' },
]
const customLabel = computed(() => {
  const match = ddOptions.find(o => o.value === selectedPeriod.value)
  return (match && selectedPeriod.value !== 'All') ? match.label : 'Custom'
})
function pickDd(opt) {
  selectedPeriod.value = opt.value
  showDd.value = false
}
onMounted(() => {
  document.addEventListener('click', (e) => {
    if (ddRef.value && !ddRef.value.contains(e.target)) showDd.value = false
  })
})

/* ── Day-of-week bar data ── */
const daybars = [
  { d:'Sun', pct:72, alert:false, alertPct:0, active:false },
  { d:'Mon', pct:58, alert:false, alertPct:0, active:false },
  { d:'Tue', pct:65, alert:false, alertPct:0, active:false },
  { d:'Wed', pct:80, alert:true,  alertPct:30, active:true  },
  { d:'Thu', pct:55, alert:false, alertPct:0, active:false },
  { d:'Fri', pct:62, alert:false, alertPct:0, active:false },
  { d:'Sat', pct:48, alert:false, alertPct:0, active:false },
]

/* ── Chart data — main line (white/balance) ── */
const pts = {
  All:    [[0,98],[50,84],[100,92],[155,65],[200,78],[255,48],[310,58],[355,10],[400,82]],
  Custom: [[0,98],[50,84],[100,92],[155,65],[200,78],[255,48],[310,58],[355,10],[400,82]],
  Days:   [[0,105],[50,90],[100,98],[155,82],[200,94],[255,75],[310,88],[355,52],[400,82]],
  Weeks:  [[0,102],[50,88],[100,95],[155,70],[200,82],[255,52],[310,66],[355,24],[400,86]],
  Months: [[0,98],[50,84],[100,92],[155,65],[200,78],[255,48],[310,58],[355,10],[400,82]],
  Years:  [[0,102],[50,96],[100,86],[155,64],[200,48],[255,32],[310,25],[355,6],[400,55]],
}

/* ── Second line data (red/spendings) ── */
const pts2 = {
  All:    [[0,115],[50,105],[100,112],[155,88],[200,100],[255,72],[310,82],[355,42],[400,105]],
  Custom: [[0,115],[50,105],[100,112],[155,88],[200,100],[255,72],[310,82],[355,42],[400,105]],
  Days:   [[0,120],[50,110],[100,118],[155,100],[200,115],[255,95],[310,108],[355,78],[400,102]],
  Weeks:  [[0,118],[50,108],[100,115],[155,92],[200,105],[255,75],[310,88],[355,55],[400,108]],
  Months: [[0,115],[50,105],[100,112],[155,88],[200,100],[255,72],[310,82],[355,42],[400,105]],
  Years:  [[0,120],[50,115],[100,108],[155,88],[200,75],[255,60],[310,55],[355,38],[400,80]],
}

function smooth(ps) {
  let d = `M${ps[0][0]},${ps[0][1]}`
  for (let i = 1; i < ps.length; i++) {
    const a = ps[i-1], b = ps[i]
    const cx = a[0] + (b[0]-a[0])*0.5
    d += ` C${cx},${a[1]} ${cx},${b[1]} ${b[0]},${b[1]}`
  }
  return d
}

const linePath  = computed(() => smooth(pts[selectedPeriod.value]))
const line2Path = computed(() => smooth(pts2[selectedPeriod.value]))
const xlabels = computed(() => ({
  All:    ['Jan','Mar','May','Jul','Sep','Nov'],
  Custom: ['Jan','Mar','May','Jul','Sep','Nov'],
  Days:   ['Mon','Tue','Wed','Thu','Fri','Sat','Sun'],
  Weeks:  ['Wk 1','Wk 2','Wk 3','Wk 4'],
  Months: ['Jan','Mar','May','Jul','Sep','Nov'],
  Years:  ['2021','2022','2023','2024','2025','2026'],
})[selectedPeriod.value])

/* ── Currency cards ── */
const currencies = [
  { code:'NGN', sym:'₦', amt:'₦260,375', chg:'+24.0%', up:true,
    route: '/wallet',
    spark:'0,22 10,18 20,20 30,15 40,13 50,10 60,7 70,5 80,11',
    area:'M0,22 L10,18 L20,20 L30,15 L40,13 L50,10 L60,7 L70,5 L80,11 L80,28 L0,28 Z' },
  { code:'USD', sym:'$', amt:'$0.00', chg:'+0.00%', up:true,
    route: '/tools/currency',
    spark:'0,18 10,17 20,18 30,18 40,17 50,18 60,18 70,17 80,18',
    area:'M0,18 L10,17 L20,18 L30,18 L40,17 L50,18 L60,18 L70,17 L80,18 L80,28 L0,28 Z' },
  { code:'EUR', sym:'€', amt:'€0.00', chg:'-0.02%', up:false,
    route: '/tools/currency',
    spark:'0,10 10,12 20,13 30,16 40,17 50,19 60,21 70,23 80,25',
    area:'M0,10 L10,12 L20,13 L30,16 L40,17 L50,19 L60,21 L70,23 L80,25 L80,28 L0,28 Z' },
]


/* ── Dot grid (12×8 = 96 dots) ── */
const dots = [
  0.06,0.55,0.06,0.80,0.06,0.08,0.75,0.06,0.08,0.06,0.55,0.06,
  0.08,0.06,0.90,0.06,0.55,0.06,0.08,0.85,0.06,0.08,0.06,0.60,
  0.55,0.06,0.08,0.06,0.80,0.06,0.08,0.06,0.70,0.06,0.08,0.06,
  0.06,0.75,0.06,0.08,0.06,0.90,0.06,0.55,0.06,0.08,0.65,0.06,
  0.08,0.06,0.60,0.06,0.08,0.06,0.85,0.06,0.08,0.75,0.06,0.08,
  0.06,0.08,0.06,0.70,0.06,0.55,0.06,0.08,0.06,0.08,0.06,0.80,
  0.65,0.06,0.08,0.06,0.75,0.06,0.08,0.06,0.55,0.06,0.08,0.06,
  0.06,0.55,0.06,0.08,0.06,0.08,0.06,0.70,0.06,0.08,0.06,0.55,
]
</script>

<style scoped>
/* ── entry animation ── */
@keyframes up { from{opacity:0;transform:translateY(20px)} to{opacity:1;transform:none} }
@keyframes pulse { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:.5;transform:scale(.75)} }

.dash { display:flex; flex-direction:column; gap:14px; }

/* ══════════════════════════════════
   SHARED CARD BASE
   ══════════════════════════════════ */
.card {
  background: rgba(255,255,255,0.09);
  backdrop-filter: blur(60px) saturate(160%) brightness(0.80);
  -webkit-backdrop-filter: blur(60px) saturate(160%) brightness(0.80);
  border: 1px solid rgba(255,255,255,0.16);
  box-shadow: 0 4px 24px rgba(0,0,0,0.3), inset 0 1px 0 rgba(255,255,255,0.1);
  border-radius: 16px;
  padding: 20px 22px;
  display: flex;
  flex-direction: column;
  gap: 14px;
  transition: border-color .22s, background .22s;
}
.card:hover { background: rgba(255,255,255,0.08); border-color: rgba(255,255,255,0.15); }

/* ══════════════════════════════════
   HERO
   ══════════════════════════════════ */
.hero {
  background: transparent;
  border: none;
  border-radius: 0;
  padding: 4px 4px 12px;
  display: flex;
  flex-direction: column;
  gap: 0;
  animation: up .5s cubic-bezier(.4,0,.2,1) .05s both;
}

.hero__lbl {
  font-size: 0.85rem;
  font-weight: 600;
  color: rgba(255,255,255,0.80);
  margin: 0 0 8px;
  letter-spacing: 0.02em;
}

.hero__row {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-bottom: 10px;
  flex-wrap: wrap;
}

.hero__amt {
  font-size: 2.7rem;
  font-weight: 800;
  color: rgba(255,255,255,0.97);
  letter-spacing: -0.04em;
  line-height: 1;
  margin: 0;
}

.hero__dec {
  font-size: 1.6rem;
  font-weight: 700;
  color: rgba(255,255,255,0.6);
  letter-spacing: -0.02em;
}

.hero__badge {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 5px 12px;
  border-radius: 8px;
  background: rgba(34,197,94,0.15);
  border: 1px solid rgba(34,197,94,0.3);
  color: #22c55e;
  font-size: 0.76rem;
  font-weight: 700;
  white-space: nowrap;
}

.hero__split {
  display: flex;
  align-items: center;
  gap: 18px;
  margin-bottom: 16px;
  flex-wrap: wrap;
}

.hero__split-item {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  font-size: 0.88rem;
  font-weight: 600;
  color: rgba(255,255,255,0.85);
  font-weight: 500;
}

/* Action buttons */
.hero__actions {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.ha {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 9px 20px;
  border-radius: 8px;
  border: 1px solid rgba(255,255,255,0.12);
  background: rgba(255,255,255,0.06);
  color: rgba(255,255,255,0.82);
  font-family: 'Space Grotesk', sans-serif;
  font-size: 0.81rem;
  font-weight: 600;
  cursor: pointer;
  transition: background .18s, border-color .18s, transform .12s;
  white-space: nowrap;
}
.ha:hover { background: rgba(255,255,255,0.1); border-color: rgba(255,255,255,0.2); transform: translateY(-1px); }
.ha--green {
  background: #22c55e;
  border-color: #22c55e;
  color: #000;
  font-weight: 700;
}
.ha--green:hover { background: #1db54f; border-color: #1db54f; }

/* ══════════════════════════════════
   CURRENCY CARDS — separate row
   ══════════════════════════════════ */
.cur-row {
  display: flex;
  gap: 12px;
  animation: up .5s cubic-bezier(.4,0,.2,1) .12s both;
}

.cur {
  flex: 0 0 200px;
  width: 200px;
  position: relative;
  overflow: hidden;
  background: rgba(255,255,255,0.07);
  backdrop-filter: blur(40px) saturate(150%) brightness(0.85);
  -webkit-backdrop-filter: blur(40px) saturate(150%) brightness(0.85);
  border: 1px solid rgba(255,255,255,0.14);
  box-shadow: 0 4px 24px rgba(0,0,0,0.3), inset 0 1px 0 rgba(255,255,255,0.1);
  border-radius: 14px;
  padding: 12px 14px 10px;
  display: flex;
  flex-direction: column;
  gap: 6px;
  transition: border-color .2s, background .2s, transform .2s;
  cursor: pointer;
}
.cur:active { transform: scale(0.98); }
.cur:hover { background: rgba(255,255,255,0.09); border-color: rgba(255,255,255,0.16); }

/* Dot-grid texture overlay like Vaulto */
.cur__texture {
  position: absolute;
  inset: 0;
  background-image: radial-gradient(circle, rgba(255,255,255,0.07) 1px, transparent 1px);
  background-size: 14px 14px;
  pointer-events: none;
  opacity: 0.6;
}

.cur__top {
  display: flex;
  align-items: center;
  gap: 8px;
  position: relative;
}

.cur__ico-wrap {
  width: 24px; height: 24px;
  border-radius: 50%;
  background: rgba(255,255,255,0.09);
  border: 1px solid rgba(255,255,255,0.13);
  display: flex; align-items: center; justify-content: center;
  flex-shrink: 0;
}

.cur__ico {
  font-size: 0.65rem;
  font-weight: 800;
  color: rgba(255,255,255,0.85);
}

.cur__code {
  font-size: 0.72rem;
  font-weight: 700;
  color: rgba(255,255,255,0.5);
  letter-spacing: 0.04em;
  position: relative;
}

.cur__amt {
  font-size: 1.25rem;
  font-weight: 800;
  color: rgba(255,255,255,0.97);
  letter-spacing: -0.03em;
  line-height: 1;
  position: relative;
}

.cur__btm {
  display: flex;
  align-items: center;
  gap: 6px;
  position: relative;
}

.cur__spark { flex: 1; height: 22px; display: block; }

.cur__chg {
  display: inline-flex;
  align-items: center;
  gap: 2px;
  font-size: 0.63rem;
  font-weight: 700;
  white-space: nowrap;
  flex-shrink: 0;
}
.cur__chg--up { color: #22c55e; }
.cur__chg--dn { color: #f87171; }

.cur__sub {
  font-size: 0.62rem;
  color: rgba(255,255,255,0.75);
  font-weight: 600;
  position: relative;
}

/* Add currency button */
.cur__add {
  flex: 0 0 150px;
  width: 150px;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 12px 10px;
  background: rgba(255,255,255,0.02);
  border: 1.5px dashed rgba(255,255,255,0.13);
  border-radius: 14px;
  cursor: pointer;
  text-align: center;
  transition: all .2s;
}
.cur__add:hover {
  background: rgba(34,197,94,0.04);
  border-color: rgba(34,197,94,0.3);
}

.cur__add-inner {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  color: rgba(255,255,255,0.28);
  font-family: 'Space Grotesk', sans-serif;
  font-size: 0.65rem;
  font-weight: 500;
  line-height: 1.5;
}
.cur__add:hover .cur__add-inner { color: rgba(255,255,255,0.5); }

/* ══════════════════════════════════
   CHARTS ROW
   ══════════════════════════════════ */
.chart-row {
  display: grid;
  grid-template-columns: 1.4fr 1fr;
  gap: 14px;
  animation: up .5s cubic-bezier(.4,0,.2,1) .2s both;
}

.chart-main, .chart-side { }

.card__head {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 12px;
}

.card__lbl {
  font-size: 0.67rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: rgba(255,255,255,0.36);
  margin: 0 0 5px;
}

.card__val {
  font-size: 1.7rem;
  font-weight: 800;
  color: rgba(255,255,255,0.95);
  letter-spacing: -0.03em;
  line-height: 1;
  display: block;
}

.period-tabs { display: flex; gap: 4px; flex-shrink: 0; align-items: center; }

.ptab {
  display: inline-flex;
  align-items: center;
  gap: 3px;
  padding: 4px 12px;
  border-radius: 7px;
  border: 1px solid transparent;
  background: none;
  color: rgba(255,255,255,0.35);
  font-family: 'Space Grotesk', sans-serif;
  font-size: 0.68rem;
  font-weight: 600;
  cursor: pointer;
  transition: all .16s;
}
.ptab:hover:not(.ptab--on) { color: rgba(255,255,255,0.6); }
.ptab--on {
  background: rgba(255,255,255,0.09);
  border-color: rgba(255,255,255,0.14);
  color: rgba(255,255,255,0.92);
}
.ptab--custom { border: 1px solid rgba(255,255,255,0.1); color: rgba(255,255,255,0.5); }

/* ── Custom dropdown ── */
.custom-dd { position: relative; }

.dd-caret {
  transition: transform 0.2s ease;
  flex-shrink: 0;
}
.dd-caret--open { transform: rotate(180deg); }

.dd-menu {
  position: absolute;
  top: calc(100% + 6px);
  right: 0;
  min-width: 170px;
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(40px) saturate(180%);
  -webkit-backdrop-filter: blur(40px) saturate(180%);
  border: 1px solid rgba(255, 255, 255, 0.15);
  border-radius: 12px;
  padding: 5px;
  z-index: 200;
}

.dd-item {
  display: flex;
  align-items: center;
  gap: 8px;
  width: 100%;
  padding: 8px 10px;
  border-radius: 8px;
  border: none;
  background: transparent;
  font-family: 'Space Grotesk', sans-serif;
  font-size: 0.78rem;
  font-weight: 500;
  color: rgba(255,255,255,0.55);
  cursor: pointer;
  text-align: left;
  transition: background 0.15s, color 0.15s;
}
.dd-item:hover { background: rgba(255,255,255,0.07); color: rgba(255,255,255,0.9); }
.dd-item--on   { color: rgba(255,255,255,0.92); }
.dd-spacer { width: 12px; flex-shrink: 0; }

/* Dropdown animation */
.dd-enter-active { transition: opacity 0.15s ease, transform 0.15s ease; }
.dd-leave-active { transition: opacity 0.1s ease, transform 0.1s ease; }
.dd-enter-from   { opacity: 0; transform: translateY(-4px); }
.dd-leave-to     { opacity: 0; transform: translateY(-4px); }


.chart-body { display: flex; flex-direction: column; gap: 6px; position: relative; }

.chart-svg { width: 100%; display: block; overflow: visible; }

/* Tooltip callout */
.chart-tooltip {
  position: absolute;
  top: 38px;
  left: 44%;
  background: rgba(28,29,44,0.96);
  border: 1px solid rgba(255,255,255,0.1);
  border-radius: 8px;
  padding: 6px 10px;
  display: flex;
  flex-direction: column;
  gap: 2px;
  pointer-events: none;
}
.ct-date { font-size: 0.6rem; color: rgba(255,255,255,0.4); font-weight: 500; }
.ct-val  { font-size: 0.68rem; color: rgba(255,255,255,0.88); font-weight: 700; }

.chart-xlabels { display: flex; justify-content: space-between; padding: 0 2px; }
.chart-xlabels span { font-size: 0.6rem; color: rgba(255,255,255,0.22); font-weight: 500; }

/* Numbered pagination dots */
.chart-dots {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 4px;
  padding-top: 2px;
  flex-wrap: wrap;
}
.chart-dot {
  width: 20px; height: 20px;
  border-radius: 50%;
  background: rgba(255,255,255,0.06);
  border: 1px solid rgba(255,255,255,0.1);
  color: rgba(255,255,255,0.3);
  font-size: 0.52rem;
  font-weight: 600;
  font-family: 'Space Grotesk', sans-serif;
  display: flex; align-items: center; justify-content: center;
  cursor: pointer;
  transition: all .18s;
}
.chart-dot:hover { background: rgba(255,255,255,0.1); color: rgba(255,255,255,0.6); }
.chart-dot--on {
  background: rgba(251,146,60,0.2);
  border-color: rgba(251,146,60,0.5);
  color: #fb923c;
}

/* Bar chart */
.see-all {
  font-size: 0.7rem;
  font-weight: 600;
  color: #22c55e;
  cursor: pointer;
  background: none;
  border: none;
  font-family: 'Space Grotesk', sans-serif;
  padding: 0;
  transition: opacity .18s;
}
.see-all:hover { opacity: .72; }

.bars-wrap {
  flex: 1;
  display: flex;
  align-items: flex-end;
  gap: 8px;
  height: 150px;
}

.bar-col {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 5px;
  height: 100%;
}

.bar-track {
  flex: 1;
  width: 100%;
  max-width: 28px;
  background: rgba(255,255,255,0.08);
  border-radius: 7px;
  position: relative;
  overflow: hidden;
}

/* bar grows from bottom */
.bar-fill {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(255,255,255,0.16);
  border-radius: 7px 7px 0 0;
  transition: height .4s cubic-bezier(.4,0,.2,1);
}

/* red alert block floats at top of bar */
.bar-alert {
  position: absolute;
  left: 0;
  right: 0;
  background: #dc2626;
  border-radius: 4px;
  transition: all .4s cubic-bezier(.4,0,.2,1);
}

.bar-lbl {
  font-size: 0.55rem;
  color: rgba(255,255,255,0.28);
  font-weight: 500;
  white-space: nowrap;
}
.bar-lbl--on {
  color: rgba(255,255,255,0.85);
  font-weight: 700;
}

/* ══════════════════════════════════
   BOTTOM ROW
   ══════════════════════════════════ */
.bot-row {
  display: grid;
  grid-template-columns: 1fr 1fr 0.75fr;
  gap: 14px;
  animation: up .5s cubic-bezier(.4,0,.2,1) .28s both;
}

/* Compact padding for all bottom cards */
.bot-activity, .bot-sankey, .bot-cashflow {
  padding: 14px 16px;
  gap: 10px;
}

.bot-head { display: flex; align-items: flex-start; justify-content: space-between; }

.bot-title { font-size: 0.8rem; font-weight: 600; color: rgba(255,255,255,0.88); }

.bot-link {
  font-size: 0.7rem; font-weight: 600; color: rgba(255,255,255,0.5);
  cursor: pointer; transition: color .18s;
}
.bot-link:hover { color: rgba(255,255,255,0.85); }

/* ── Activity list ── */
.act-list { display: flex; flex-direction: column; gap: 1px; }

.act-item {
  display: flex; align-items: center; gap: 9px;
  padding: 5px 4px; border-radius: 10px; transition: background .14s;
}
.act-item:hover { background: rgba(255,255,255,0.03); }

.act-icon {
  width: 32px; height: 32px; border-radius: 50%;
  display: flex; align-items: center; justify-content: center; flex-shrink: 0;
  background: rgba(255,255,255,0.06);
  border: 1px solid rgba(255,255,255,0.08);
}
.act-num {
  font-size: 0.6rem; font-weight: 800;
  color: rgba(255,255,255,0.6);
  font-family: 'Space Grotesk', sans-serif;
}

.act-info { flex: 1; display: flex; flex-direction: column; gap: 2px; min-width: 0; }

.act-name {
  font-size: 0.75rem; font-weight: 600; color: rgba(255,255,255,0.88);
  white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
}
.act-date { font-size: 0.62rem; color: rgba(255,255,255,0.28); }

.act-amt { font-size: 0.74rem; font-weight: 600; white-space: nowrap; flex-shrink: 0; }
.act-amt--pos { color: rgba(34,197,94,0.9); }
.act-amt--neg { color: rgba(248,113,113,0.9); }

/* ── Sankey (Total Assets) ── */
.sankey-val {
  font-size: 1.45rem; font-weight: 800;
  color: rgba(255,255,255,0.95); letter-spacing: -0.03em; line-height: 1;
}

.sankey-wrap {
  flex: 1;
  display: flex;
  align-items: center;
  gap: 0;
  min-height: 100px;
  max-height: 140px;
  margin-top: 12px;
}

.sankey-svg { flex: 1; width: 100%; height: 100%; min-height: 80px; display: block; }

.sankey-col { display: flex; flex-direction: column; justify-content: space-between; gap: 6px; flex-shrink: 0; }

.sankey-center { flex-shrink: 0; display: flex; align-items: center; }

.snode {
  display: flex; flex-direction: column; gap: 1px;
  background: rgba(255,255,255,0.07); border: 1px solid rgba(255,255,255,0.1);
  border-radius: 8px; padding: 5px 8px;
}
.snode--sm { min-width: 50px; }
.snode--accent {
  background: rgba(220,38,38,0.25); border-color: rgba(220,38,38,0.5);
  padding: 7px 10px; border-radius: 9px;
}

.snode-sym { font-size: 0.55rem; color: rgba(255,255,255,0.4); font-weight: 700; }
.snode-lbl { font-size: 0.62rem; color: rgba(255,255,255,0.65); font-weight: 700; }
.snode-amt { font-size: 0.58rem; color: rgba(255,255,255,0.35); font-weight: 500; }
.snode-amt2 { font-size: 0.82rem; color: rgba(255,255,255,0.92); font-weight: 800; }

/* ── Net Cashflow ── */
.stat-lbl {
  font-size: 0.63rem; font-weight: 700; letter-spacing: 0.09em;
  text-transform: uppercase; color: rgba(255,255,255,0.36); margin: 0;
}

.stat-val {
  font-size: 1.5rem; font-weight: 800;
  color: rgba(255,255,255,0.95); letter-spacing: -0.03em; line-height: 1;
  display: block; margin-top: 4px;
}
.stat-val--neg { color: #f87171; }

.cf-badge {
  font-size: 0.62rem; font-weight: 700; padding: 3px 8px; border-radius: 6px;
  background: rgba(248,113,113,0.12); color: #f87171;
  border: 1px solid rgba(248,113,113,0.25); flex-shrink: 0;
}

/* Dot heatmap grid */
.dot-grid {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  grid-auto-rows: 1fr;
  gap: 3px;
  width: 100%;
  aspect-ratio: 3 / 2;
}
.dot {
  border-radius: 2px;
  background: rgba(255,255,255,1);
}

/* ══════════════════════════════════
   RESPONSIVE
   ══════════════════════════════════ */

@media (max-width:1100px) {
  .cur-row    { overflow-x: auto; padding-bottom: 4px; -webkit-overflow-scrolling: touch; }
  .cur-row::-webkit-scrollbar { display: none; }
  .cur__add   { display: none; }
  .chart-row  { grid-template-columns: 1fr; }
  .chart-side { display: none; }
  .bot-row    { grid-template-columns: 1fr 1fr; }
}

@media (max-width:768px) {
  .hero__amt    { font-size: 2rem; }
  .hero__dec    { font-size: 1.1rem; }
  .ha           { padding: 9px 16px; font-size: 0.78rem; }
  .bot-row      { grid-template-columns: 1fr; gap: 10px; }
  .card         { padding: 16px; }
  .sankey-wrap  { min-height: 130px; max-height: 160px; margin-top: 8px; }
  .sankey-svg   { min-height: 120px; }
  .snode--sm    { min-width: 46px; padding: 4px 6px; }
  .snode-lbl    { font-size: 0.58rem; }
  .snode-amt    { font-size: 0.52rem; }
  .snode-amt2   { font-size: 0.72rem; }
  .snode--accent { padding: 6px 8px; }
}

@media (max-width:600px) {
  .hero__amt  { font-size: 1.75rem; }
  .hero__dec  { font-size: 0.98rem; }
  .ha         { padding: 8px 14px; font-size: 0.75rem; gap: 5px; }
  .card       { padding: 14px 14px; }
}

@media (max-width:430px) {
  .hero__amt  { font-size: 1.5rem; }
  .hero__dec  { font-size: 0.88rem; }
  .ha         { padding: 7px 12px; font-size: 0.72rem; }
}

@media (max-width:360px) {
  .hero__amt  { font-size: 1.3rem; }
  .hero__dec  { font-size: 0.8rem; }
  .card       { padding: 12px 12px; }
}

</style>
