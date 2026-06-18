<!-- src/views/TransactionsTrackerView.vue -->
<template>
  <div class="tt-page">

    <!-- Page Header -->
    <div class="page-header">
      <h1 class="page-header__title">Transactions Trackers</h1>
      <p class="page-header__sub">Choose a receipt style, fill in the details, and share it with a link.</p>
    </div>

    <!-- Cards -->
    <div class="service-grid">
      <div
        v-for="s in services"
        :key="s.title"
        class="service-card"
        @click="$router.push(s.route)"
      >
        <div class="service-card__icon">
          <svg width="22" height="22" viewBox="0 0 24 24" fill="none"
            stroke="#22c55e" stroke-width="1.8"
            stroke-linecap="round" stroke-linejoin="round"
            v-html="s.icon"
          />
        </div>
        <h2 class="service-card__title">{{ s.title }}</h2>
        <p class="service-card__desc" v-html="s.desc" />
      </div>
    </div>

  </div>
</template>

<script setup>
const services = [
  {
    title: 'Receipts Tracking',
    desc:  'A step-by-step receipt with <span class="hl">a status icon</span>, <span class="hl">a progress timeline</span> and <span class="hl">an action button</span> — share it with a link.',
    route: '/tools/transactions-tracker/receipts',
    icon:  '<path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/><line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/>',
  },
  {
    title: 'Payment Tracking',
    desc:  'A live receipt with <span class="hl">a spinning status</span>, <span class="hl">amounts</span> and <span class="hl">copyable details</span> — share it with a link.',
    route: '/tools/transactions-tracker/payment',
    icon:  '<polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/>',
  },
]
</script>

<style scoped>
.tt-page {
  display: flex;
  flex-direction: column;
  gap: 28px;
}

/* Header */
.page-header        { display: flex; flex-direction: column; gap: 6px; }
.page-header__title {
  font-size: 1.5rem; font-weight: 800; color: var(--t1);
  letter-spacing: -0.025em; margin: 0;
}
.page-header__sub { font-size: 0.82rem; color: var(--t3); margin: 0; }

/* Grid */
.service-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 18px;
}

/* Card */
.service-card {
  background: var(--glass);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  border: 1px solid var(--border-soft);
  border-radius: 16px;
  padding: 24px 22px;
  display: flex;
  flex-direction: column;
  gap: 12px;
  cursor: pointer;
  transition: border-color 0.22s ease, transform 0.18s ease, box-shadow 0.22s ease;
}
.service-card:hover {
  border-color: var(--accent-border);
  transform: translateY(-3px);
  box-shadow: 0 8px 28px rgba(34, 197, 94, 0.08);
}

.service-card__icon {
  width: 46px; height: 46px; border-radius: 13px;
  background: rgba(34, 197, 94, 0.12);
  border: 1px solid rgba(34, 197, 94, 0.18);
  display: flex; align-items: center; justify-content: center; flex-shrink: 0;
}

.service-card__title {
  font-size: 1rem; font-weight: 700; color: var(--t1); margin: 0;
}

.service-card__desc {
  font-size: 0.82rem; color: var(--t3); line-height: 1.6; margin: 0;
}
.service-card__desc :deep(.hl) { color: var(--accent); font-weight: 500; }

/* Responsive */
@media (max-width: 768px) {
  .service-grid { grid-template-columns: 1fr; gap: 12px; }
  .service-card { padding: 20px 18px; }
}

/* Light mode */
[data-theme="light"] .service-card {
  background: #fff;
  border-color: rgba(0,0,0,0.08);
  box-shadow: 0 2px 12px rgba(0,0,0,0.05);
}
[data-theme="light"] .service-card:hover {
  box-shadow: 0 8px 28px rgba(34,197,94,0.1);
}
[data-theme="light"] .service-card__icon {
  background: rgba(34, 197, 94, 0.1);
  border-color: rgba(34, 197, 94, 0.2);
}
</style>
