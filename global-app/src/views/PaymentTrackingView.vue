<!-- src/views/PaymentTrackingView.vue -->
<template>
  <div class="pt-page">

    <!-- Page Header -->
    <div class="pt-header">
      <div class="pt-icon-box">
        <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="#22c55e" stroke-width="2" stroke-linecap="round">
          <rect x="1" y="4" width="22" height="16" rx="2"/><line x1="1" y1="10" x2="23" y2="10"/>
        </svg>
      </div>
      <div>
        <h1 class="pt-title">Payment Tracking</h1>
        <p class="pt-sub">Create a live tracking receipt and share it <span class="pt-hl">instantly</span> with a link.</p>
      </div>
    </div>

    <!-- Two-column layout -->
    <div class="pt-layout">

      <!-- ── LEFT: Form ── -->
      <div class="pt-form-col">

        <!-- 1. Service Customization -->
        <div class="sec-card">
          <div class="sec-head">
            <svg class="sec-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><circle cx="12" cy="12" r="10"/><line x1="2" y1="12" x2="22" y2="12"/><path d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"/></svg>
            <h3 class="sec-title">Service Customization</h3>
          </div>
          <div class="two-col">
            <div class="fg">
              <label class="fl">Service Name</label>
              <input v-model="form.serviceName" type="text" placeholder="e.g. Bitcoin, Binance, PayPal" class="fi" />
            </div>
            <div class="fg">
              <label class="fl">Primary Color</label>
              <div class="color-field">
                <label class="color-swatch-wrap" :style="{ background: form.primaryColor }">
                  <input type="color" v-model="form.primaryColor" class="color-native" />
                </label>
                <input v-model="form.primaryColor" type="text" class="fi color-hex" @blur="validateHex" placeholder="#22c55e" />
              </div>
            </div>
          </div>
          <div class="fg">
            <label class="fl">Logo</label>
            <div class="logo-row">
              <div class="logo-preview-box">
                <img v-if="logoUrl" :src="logoUrl" class="logo-img" alt="logo" />
                <svg v-else width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"><rect x="3" y="3" width="18" height="18" rx="2"/><circle cx="8.5" cy="8.5" r="1.5"/><polyline points="21 15 16 10 5 21"/></svg>
              </div>
              <label class="logo-drop" @dragover.prevent @drop.prevent="e => handleDrop(e,'logo')">
                <input type="file" accept="image/*" hidden ref="logoRef" @change="e => handleFile(e,'logo')" />
                <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><polyline points="16 16 12 12 8 16"/><line x1="12" y1="12" x2="12" y2="21"/><path d="M20.39 18.39A5 5 0 0 0 18 9h-1.26A8 8 0 1 0 3 16.3"/></svg>
                <span>Drag & drop or <button type="button" class="click-link" @click.prevent="logoRef.click()">click</button></span>
              </label>
              <div class="mode-pills" style="margin-left:auto">
                <span class="fl" style="margin-right:8px">Receipt mode</span>
                <button :class="['mode-pill', { 'mode-pill--active': form.receiptMode === 'light' }]" @click="form.receiptMode = 'light'">
                  <svg width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><circle cx="12" cy="12" r="5"/><line x1="12" y1="1" x2="12" y2="3"/><line x1="12" y1="21" x2="12" y2="23"/><line x1="4.22" y1="4.22" x2="5.64" y2="5.64"/><line x1="18.36" y1="18.36" x2="19.78" y2="19.78"/><line x1="1" y1="12" x2="3" y2="12"/><line x1="21" y1="12" x2="23" y2="12"/></svg>
                  Light
                </button>
                <button :class="['mode-pill', { 'mode-pill--active': form.receiptMode === 'dark' }]" :style="form.receiptMode === 'dark' ? { background: form.primaryColor, borderColor: form.primaryColor } : {}" @click="form.receiptMode = 'dark'">
                  <svg width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"/></svg>
                  Dark
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- 2. Receipt Link -->
        <div class="sec-card">
          <div class="sec-head">
            <svg class="sec-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><circle cx="12" cy="12" r="10"/><line x1="2" y1="12" x2="22" y2="12"/><path d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"/></svg>
            <h3 class="sec-title">Receipt Link</h3>
          </div>
          <p class="sec-desc">Pick a name — your receipt gets its own clean link on one of our domains. <span class="hl">Leave blank to use a short auto link instead.</span></p>
          <p class="fl" style="margin:0">Your receipt link</p>
          <div class="subdomain-box">
            <div class="subdomain-title-row">
              <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="#22c55e" stroke-width="2" stroke-linecap="round"><path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"/></svg>
              <span class="subdomain-name">Use our subdomain</span>
              <span class="subdomain-free">free</span>
            </div>
            <p class="subdomain-hint">Pick a keyword — we set up the link for you. <span class="hl">Nothing</span> to configure at your domain.</p>
            <div class="slug-row">
              <input v-model="form.receiptSlug" type="text" placeholder="payment-receipt" class="fi slug-input" />
              <select v-model="form.domain" class="fs slug-domain">
                <option>.qst1x.com</option>
                <option>.cryptotrack.app</option>
                <option>.receiptlive.io</option>
              </select>
            </div>
            <p class="slug-note">Lowercase letters, numbers and dashes — then <span class="hl">pick a domain</span>. <span class="hl">Locked in once created.</span></p>
          </div>
        </div>

        <!-- 3. Payment Details -->
        <div class="sec-card">
          <div class="sec-head">
            <svg class="sec-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><line x1="12" y1="1" x2="12" y2="23"/><path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"/></svg>
            <h3 class="sec-title">Payment Details</h3>
          </div>
          <div class="two-col">
            <div class="fg">
              <label class="fl">Payment Type</label>
              <input v-model="form.paymentType" type="text" placeholder="e.g. Bitcoin (BTC)" class="fi" />
            </div>
            <div class="fg">
              <label class="fl">Status</label>
              <select v-model="form.status" class="fi fs">
                <option>Pending</option>
                <option>Processing</option>
                <option>Complete</option>
                <option>Failed</option>
                <option>Cancelled</option>
              </select>
            </div>
          </div>
          <div class="two-col">
            <div class="fg">
              <label class="fl">Progress %</label>
              <input v-model="form.progress" type="number" min="0" max="100" placeholder="89" class="fi" />
            </div>
            <div class="fg">
              <label class="fl">Status Message</label>
              <input v-model="form.statusMessage" type="text" placeholder="Loading... Let's keep it running" class="fi" />
            </div>
          </div>
          <div class="two-col">
            <div class="fg">
              <label class="fl">Amount (Local Currency)</label>
              <input v-model="form.amountLocal" type="text" placeholder="21,515,900.00" class="fi" />
            </div>
            <div class="fg">
              <label class="fl">Local Currency Name</label>
              <input v-model="form.currencyName" type="text" placeholder="Philippine pesos" class="fi" />
            </div>
          </div>
          <div class="two-col">
            <div class="fg">
              <label class="fl">Amount in Crypto</label>
              <input v-model="form.amountCrypto" type="text" placeholder="4.5 BTC" class="fi" />
            </div>
            <div class="fg">
              <label class="fl">Amount in USD</label>
              <input v-model="form.amountUSD" type="text" placeholder="$350,000" class="fi" />
            </div>
          </div>
          <div class="two-col">
            <div class="fg">
              <label class="fl">Recipient (To)</label>
              <input v-model="form.recipient" type="text" placeholder="Recipient name" class="fi" />
            </div>
            <div class="fg">
              <label class="fl">Note</label>
              <input v-model="form.note" type="text" placeholder="From profit" class="fi" />
            </div>
          </div>
          <div class="two-col">
            <div class="fg">
              <label class="fl">Network Fee</label>
              <input v-model="form.networkFee" type="text" placeholder="$760" class="fi" />
            </div>
            <div class="fg">
              <label class="fl">Fee Note</label>
              <input v-model="form.feeNote" type="text" placeholder="Pay to your BTC-Card" class="fi" />
            </div>
          </div>
          <div class="fg">
            <label class="fl">Date & Time</label>
            <input v-model="form.dateTime" type="text" :placeholder="defaultDateTime" class="fi" />
            <span class="field-hint">Type any date/time format <span class="hl">you like.</span></span>
          </div>

          <!-- Copyable Details -->
          <div class="sec-divider" />
          <div class="list-header">
            <span class="fl">Copyable details</span>
            <button class="add-btn" @click="copyFields.push({ label: '', value: '' })">+ Add field</button>
          </div>
          <div v-if="copyFields.length" class="dynamic-list">
            <div v-for="(f, i) in copyFields" :key="i" class="dynamic-row">
              <input v-model="f.label" type="text" class="fi" placeholder="Label (e.g. Wallet address)" style="flex:0.65" />
              <input v-model="f.value" type="text" class="fi" placeholder="Value" />
              <button class="remove-btn" @click="copyFields.splice(i,1)">
                <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
              </button>
            </div>
          </div>
        </div>

        <!-- 4. Images & Media -->
        <div class="sec-card">
          <div class="sec-head">
            <svg class="sec-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><rect x="1" y="4" width="22" height="16" rx="2"/><polyline points="1 10 23 10"/></svg>
            <h3 class="sec-title">Images & Media</h3>
          </div>
          <div class="two-col">
            <div class="fg">
              <label class="fl">QR Code Image</label>
              <div class="logo-row">
                <div class="logo-preview-box">
                  <img v-if="qrUrl" :src="qrUrl" class="logo-img" alt="qr" />
                  <svg v-else width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"><rect x="3" y="3" width="18" height="18" rx="2"/><circle cx="8.5" cy="8.5" r="1.5"/><polyline points="21 15 16 10 5 21"/></svg>
                </div>
                <label class="logo-drop" @dragover.prevent @drop.prevent="e => handleDrop(e,'qr')">
                  <input type="file" accept="image/*" hidden ref="qrRef" @change="e => handleFile(e,'qr')" />
                  <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><polyline points="16 16 12 12 8 16"/><line x1="12" y1="12" x2="12" y2="21"/><path d="M20.39 18.39A5 5 0 0 0 18 9h-1.26A8 8 0 1 0 3 16.3"/></svg>
                  <span>Drag & drop or <button type="button" class="click-link" @click.prevent="qrRef.click()">click</button></span>
                </label>
              </div>
            </div>
            <div class="fg">
              <label class="fl">Card / Promo Image</label>
              <div class="logo-row">
                <div class="logo-preview-box">
                  <img v-if="promoUrl" :src="promoUrl" class="logo-img" alt="promo" />
                  <svg v-else width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"><rect x="3" y="3" width="18" height="18" rx="2"/><circle cx="8.5" cy="8.5" r="1.5"/><polyline points="21 15 16 10 5 21"/></svg>
                </div>
                <label class="logo-drop" @dragover.prevent @drop.prevent="e => handleDrop(e,'promo')">
                  <input type="file" accept="image/*" hidden ref="promoRef" @change="e => handleFile(e,'promo')" />
                  <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><polyline points="16 16 12 12 8 16"/><line x1="12" y1="12" x2="12" y2="21"/><path d="M20.39 18.39A5 5 0 0 0 18 9h-1.26A8 8 0 1 0 3 16.3"/></svg>
                  <span>Drag & drop or <button type="button" class="click-link" @click.prevent="promoRef.click()">click</button></span>
                </label>
              </div>
            </div>
          </div>
        </div>

        <!-- 5. Billing Warning -->
        <div class="sec-card">
          <div class="sec-head-row">
            <div class="sec-head" style="flex:1">
              <svg class="sec-icon sec-icon--warn" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg>
              <h3 class="sec-title">Billing Warning</h3>
            </div>
            <button :class="['toggle-switch', { 'toggle-switch--on': form.billingWarningEnabled }]"
              :style="form.billingWarningEnabled ? { background: form.primaryColor } : {}"
              @click="form.billingWarningEnabled = !form.billingWarningEnabled" />
          </div>
          <p class="sec-desc">Turn this on to show a <span class="hl">highlighted warning box</span> on the receipt.</p>
          <Transition name="expand">
            <textarea v-if="form.billingWarningEnabled" v-model="form.billingWarningText"
              class="fi-area" rows="3" placeholder="Describe the billing warning..."></textarea>
          </Transition>
        </div>

        <!-- 6. Contact & Social Links -->
        <div class="sec-card">
          <div class="sec-head">
            <svg class="sec-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><circle cx="12" cy="12" r="10"/><line x1="2" y1="12" x2="22" y2="12"/><path d="M12 2a15.3 15.3 0 0 1 4 10 15.3 15.3 0 0 1-4 10 15.3 15.3 0 0 1-4-10 15.3 15.3 0 0 1 4-10z"/></svg>
            <h3 class="sec-title">Contact & Social Links</h3>
          </div>
          <div class="list-header">
            <span class="fl">Contact / Social Links</span>
            <button class="add-btn" @click="socialLinks.push({ label: '', url: '' })">+ Add Link</button>
          </div>
          <p class="sec-desc">Add <span class="hl">WhatsApp</span>, <span class="hl">Telegram</span>, a website — any label and link.</p>
          <div v-if="socialLinks.length" class="dynamic-list">
            <div v-for="(lnk, i) in socialLinks" :key="i" class="dynamic-row">
              <input v-model="lnk.label" type="text" class="fi" placeholder="Label (e.g. WhatsApp)" style="flex:0.6" />
              <input v-model="lnk.url" type="text" class="fi" placeholder="URL or number" />
              <button class="remove-btn" @click="socialLinks.splice(i,1)">
                <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
              </button>
            </div>
          </div>
          <div class="toggle-row" style="margin-top:4px">
            <div class="toggle-info">
              <div class="toggle-label-row">
                <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/></svg>
                <span class="fl">Live chat on the receipt</span>
              </div>
              <p class="sec-desc">Adds a real chat box. The <span class="hl">recipient messages you</span>; you reply from <span class="hl">"Manage"</span>.</p>
            </div>
            <button :class="['toggle-switch', { 'toggle-switch--on': form.liveChatEnabled }]"
              :style="form.liveChatEnabled ? { background: form.primaryColor } : {}"
              @click="form.liveChatEnabled = !form.liveChatEnabled" />
          </div>
          <div class="toggle-row">
            <div class="toggle-info">
              <span class="fl">External chat link instead</span>
              <p class="sec-desc">Only used when the in-app chat above is off.</p>
            </div>
            <button :class="['toggle-switch', { 'toggle-switch--on': form.externalChatEnabled }]"
              :style="form.externalChatEnabled ? { background: form.primaryColor } : {}"
              @click="form.externalChatEnabled = !form.externalChatEnabled" />
          </div>
        </div>

        <!-- 7. Footer -->
        <div class="sec-card">
          <div class="sec-head">
            <svg class="sec-icon" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/></svg>
            <h3 class="sec-title">Footer</h3>
          </div>
          <div class="two-col">
            <div class="fg">
              <label class="fl">Footer Text</label>
              <input v-model="form.footerText" type="text" placeholder="Secure · Fast · Decentralized" class="fi" />
            </div>
            <div class="fg">
              <label class="fl">Thank You Text</label>
              <input v-model="form.thankYouText" type="text" placeholder="Thank you!" class="fi" />
            </div>
          </div>
        </div>

        <!-- Error -->
        <p v-if="formError" class="form-error">{{ formError }}</p>

        <!-- Generate Button -->
        <button :class="['generate-btn', { 'generate-btn--loading': generating }]"
          :style="{ background: form.primaryColor }" @click="generate">
          <template v-if="!generating">
            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"/></svg>
            Generate Receipt
          </template>
          <template v-else>
            <span class="spinner" /> Generating...
          </template>
        </button>

        <!-- My tracking receipts -->
        <div class="my-receipts-card">
          <h3 class="my-receipts-title">My tracking receipts</h3>
          <div v-if="generatedReceipts.length === 0" class="my-receipts-empty">
            <p>Your generated <span class="hl">receipts</span> will appear here.</p>
          </div>
          <div v-else class="receipts-list">
            <div v-for="r in generatedReceipts" :key="r.id" class="receipt-item">
              <div class="receipt-item__dot" :style="{ background: r.color }" />
              <div class="receipt-item__info">
                <span class="receipt-item__name">{{ r.service }}</span>
                <span class="receipt-item__meta">{{ r.slug }}{{ r.domain }} · {{ r.time }}</span>
              </div>
              <span class="receipt-item__badge" :style="{ color: statusColor(r.status), background: statusBg(r.status), borderColor: statusColor(r.status) }">{{ r.status }}</span>
            </div>
          </div>
        </div>

      </div>

      <!-- ── RIGHT: Live Preview ── -->
      <div class="pt-preview-col">
        <p class="preview-label">LIVE PREVIEW</p>
        <div class="receipt-preview" :style="previewCardStyle">

          <!-- Service icon circle -->
          <div class="rp-check-circle" :style="{ borderColor: form.primaryColor }">
            <svg v-if="!logoUrl" width="22" height="22" viewBox="0 0 24 24" fill="none"
              :stroke="form.primaryColor" stroke-width="2.5" stroke-linecap="round">
              <polyline points="20 6 9 17 4 12"/>
            </svg>
            <img v-else :src="logoUrl" class="rp-logo-img" />
          </div>

          <!-- Service name -->
          <p class="rp-service-name">
            {{ (form.serviceName || 'TRANSACTIONS TRACKERS').toUpperCase() }}
          </p>

          <!-- Progress spinner row -->
          <div class="rp-progress-row">
            <div class="rp-spinner-wrap">
              <!-- Rotating dotted ring -->
              <svg class="rp-spin-svg" width="64" height="64" viewBox="0 0 64 64">
                <circle cx="32" cy="32" r="27" fill="none"
                  :stroke="form.primaryColor" stroke-width="3"
                  stroke-dasharray="4 5" stroke-linecap="round"/>
                <circle cx="32" cy="5" r="2.5" fill="#ef4444"/>
                <circle cx="59" cy="23" r="2" fill="#f97316"/>
              </svg>
              <!-- Center initial (static) -->
              <div class="rp-initial" :style="{ background: form.primaryColor }">
                {{ (form.serviceName || 'T')[0].toUpperCase() }}
              </div>
            </div>
            <span class="rp-status-text" :style="{ color: form.primaryColor }">
              {{ form.status }}
            </span>
          </div>

          <!-- Progress bar -->
          <div v-if="form.progress" class="rp-progress-bar-wrap">
            <div class="rp-progress-bar-track">
              <div class="rp-progress-bar-fill"
                :style="{ width: Math.min(Number(form.progress)||0, 100) + '%', background: form.primaryColor }" />
            </div>
            <span class="rp-progress-pct" :style="{ color: form.primaryColor }">{{ form.progress }}%</span>
          </div>

          <!-- Divider -->
          <div class="rp-divider" />

          <!-- Status row -->
          <div class="rp-meta-row">
            <span class="rp-meta-label">Status</span>
            <span class="rp-status-badge" :style="{ background: statusColor(form.status) }">
              {{ form.status.toUpperCase() }}
            </span>
          </div>

          <!-- Amount row -->
          <div v-if="form.amountCrypto || form.amountUSD" class="rp-meta-row">
            <span class="rp-meta-label">Amount</span>
            <span class="rp-meta-value">{{ form.amountCrypto || form.amountUSD }}</span>
          </div>

          <!-- Live chat -->
          <button v-if="form.liveChatEnabled" class="rp-chat-btn" :style="{ background: form.primaryColor }">
            <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/></svg>
            Live chat
          </button>

          <!-- Footer -->
          <div class="rp-footer-row">
            <span class="rp-footer-text">{{ form.footerText || 'Secure · Fast · Decentralized' }}</span>
            <span class="rp-thank-you" :style="{ color: form.primaryColor }">{{ form.thankYouText || 'Thank you!' }}</span>
          </div>

        </div>
      </div>

    </div>

    <!-- Success Toast -->
    <Transition name="toast-pop">
      <div v-if="showSuccess" class="success-toast" :style="{ background: form.primaryColor }">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"><polyline points="20 6 9 17 4 12"/></svg>
        Receipt generated successfully!
      </div>
    </Transition>

  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue'

const logoRef  = ref(null)
const qrRef    = ref(null)
const promoRef = ref(null)

const logoUrl  = ref(null)
const qrUrl    = ref(null)
const promoUrl = ref(null)

const generating      = ref(false)
const showSuccess     = ref(false)
const formError       = ref('')
const copyFields      = ref([])
const socialLinks     = ref([])
const generatedReceipts = ref([])

const defaultDateTime = new Date().toLocaleDateString('en-US', { month:'long', day:'numeric', year:'numeric' })
  + ' • ' + new Date().toLocaleTimeString('en-US', { hour:'2-digit', minute:'2-digit' })

const form = reactive({
  serviceName:          '',
  primaryColor:         '#22c55e',
  receiptMode:          'dark',
  receiptSlug:          '',
  domain:               '.qst1x.com',
  paymentType:          '',
  status:               'Pending',
  progress:             '',
  statusMessage:        '',
  amountLocal:          '',
  currencyName:         '',
  amountCrypto:         '',
  amountUSD:            '',
  recipient:            '',
  note:                 '',
  networkFee:           '',
  feeNote:              '',
  dateTime:             '',
  billingWarningEnabled:false,
  billingWarningText:   '',
  liveChatEnabled:      true,
  externalChatEnabled:  false,
  footerText:           'Secure · Fast · Decentralized',
  thankYouText:         'Thank you!',
})

const previewCardStyle = computed(() => {
  if (form.receiptMode === 'light') {
    return { background: '#f8fafc', color: '#0f172a' }
  }
  return { background: '#080e0a' }
})

const STATUS_COLORS = {
  Pending:    '#d97706',
  Processing: '#3b82f6',
  Complete:   '#16a34a',
  Failed:     '#dc2626',
  Cancelled:  '#6b7280',
}

function statusColor(s) { return STATUS_COLORS[s] || '#d97706' }
function statusBg(s) {
  const c = statusColor(s)
  return c + '18'
}

function handleFile(e, type) {
  const file = e.target.files[0]
  if (!file) return
  const reader = new FileReader()
  reader.onload = ev => {
    if (type === 'logo')  logoUrl.value  = ev.target.result
    if (type === 'qr')    qrUrl.value    = ev.target.result
    if (type === 'promo') promoUrl.value = ev.target.result
  }
  reader.readAsDataURL(file)
}
function handleDrop(e, type) {
  const file = e.dataTransfer.files[0]
  if (!file || !file.type.startsWith('image/')) return
  const reader = new FileReader()
  reader.onload = ev => {
    if (type === 'logo')  logoUrl.value  = ev.target.result
    if (type === 'qr')    qrUrl.value    = ev.target.result
    if (type === 'promo') promoUrl.value = ev.target.result
  }
  reader.readAsDataURL(file)
}

function validateHex() {
  if (!/^#[0-9a-fA-F]{6}$/.test(form.primaryColor)) {
    form.primaryColor = '#22c55e'
  }
}

function generate() {
  formError.value = ''
  if (!form.serviceName.trim())  { formError.value = 'Service name is required.'; return }
  if (!form.amountCrypto && !form.amountUSD) { formError.value = 'Please enter at least one amount field.'; return }

  generating.value = true
  setTimeout(() => {
    generating.value = false
    showSuccess.value = true
    generatedReceipts.value.unshift({
      id:      Date.now(),
      service: form.serviceName,
      slug:    form.receiptSlug || 'receipt-' + Date.now().toString(36),
      domain:  form.domain,
      color:   form.primaryColor,
      status:  form.status,
      time:    new Date().toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit' }),
    })
    setTimeout(() => { showSuccess.value = false }, 3500)
  }, 1800)
}
</script>

<style scoped>
.pt-page { display: flex; flex-direction: column; gap: 24px; }

/* Header */
.pt-header { display: flex; align-items: center; gap: 14px; }
.pt-icon-box {
  width: 52px; height: 52px; border-radius: 14px; flex-shrink: 0;
  background: rgba(34,197,94,0.15); border: 1px solid rgba(34,197,94,0.3);
  display: flex; align-items: center; justify-content: center;
}
.pt-title { font-size: 1.45rem; font-weight: 800; color: var(--t1); margin: 0; letter-spacing: -0.025em; }
.pt-sub   { font-size: 0.82rem; color: var(--t3); margin: 0; }
.pt-hl    { color: #22c55e; font-weight: 500; }

/* Layout */
.pt-layout {
  display: grid;
  grid-template-columns: 1fr 360px;
  gap: 24px;
  align-items: start;
}

/* Form column */
.pt-form-col { display: flex; flex-direction: column; gap: 16px; }

/* Section cards — always white */
.sec-card {
  background: #fff; border: 1px solid #e8ecf0; border-radius: 14px;
  padding: 20px 22px; display: flex; flex-direction: column; gap: 14px;
  box-shadow: 0 1px 6px rgba(0,0,0,0.05);
}
.sec-head     { display: flex; align-items: center; gap: 8px; }
.sec-head-row { display: flex; align-items: center; justify-content: space-between; }
.sec-icon     { color: #22c55e; flex-shrink: 0; }
.sec-icon--warn { color: #f59e0b; }
.sec-title    { font-size: 0.9rem; font-weight: 700; color: #0f172a; margin: 0; }
.sec-desc     { font-size: 0.78rem; color: #64748b; line-height: 1.6; margin: 0; }
.sec-desc .hl { color: #22c55e; font-weight: 500; }
.sec-divider  { border: none; border-top: 1px solid #f1f5f9; margin: 0; }

/* Fields */
.two-col  { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; }
.fg       { display: flex; flex-direction: column; gap: 6px; }
.fl       { font-size: 0.78rem; font-weight: 600; color: #334155; }
.field-hint { font-size: 0.72rem; color: #94a3b8; margin-top: 3px; }
.field-hint .hl { color: #22c55e; }

.fi {
  width: 100%; padding: 9px 12px; border-radius: 9px;
  border: 1px solid #e2e8f0; background: #fff; color: #0f172a;
  font-family: 'Outfit', sans-serif; font-size: 0.82rem;
  transition: border-color 0.18s; box-sizing: border-box;
}
.fi:focus { outline: none; border-color: #22c55e; }
.fi::placeholder { color: #94a3b8; }
.fs { cursor: pointer; }

.fi-area {
  width: 100%; padding: 9px 12px; border-radius: 9px;
  border: 1px solid #e2e8f0; background: #fff; color: #0f172a;
  font-family: 'Outfit', sans-serif; font-size: 0.82rem;
  resize: vertical; min-height: 80px;
  transition: border-color 0.18s; box-sizing: border-box;
}
.fi-area:focus { outline: none; border-color: #22c55e; }
.fi-area::placeholder { color: #94a3b8; }

/* Expand transition */
.expand-enter-active { transition: max-height 0.28s ease, opacity 0.22s ease; overflow: hidden; max-height: 200px; }
.expand-leave-active { transition: max-height 0.22s ease, opacity 0.18s ease; overflow: hidden; max-height: 200px; }
.expand-enter-from,
.expand-leave-to     { max-height: 0; opacity: 0; }

/* Logo row */
.logo-row        { display: flex; gap: 8px; align-items: center; flex-wrap: wrap; }
.logo-preview-box {
  width: 60px; height: 60px; border-radius: 10px; flex-shrink: 0;
  border: 1px solid #e2e8f0; background: #f8fafc;
  display: flex; align-items: center; justify-content: center; color: #94a3b8; overflow: hidden;
}
.logo-img   { width: 100%; height: 100%; object-fit: contain; }
.logo-drop  {
  flex: 1; min-width: 120px; display: flex; align-items: center; justify-content: center; gap: 7px;
  border: 1.5px dashed #cbd5e1; border-radius: 10px; padding: 10px;
  font-size: 0.75rem; color: #64748b; cursor: pointer; transition: border-color 0.18s;
}
.logo-drop:hover  { border-color: #22c55e; }
.click-link {
  background: none; border: none; padding: 0; color: #22c55e;
  font-size: 0.75rem; font-weight: 600; cursor: pointer; font-family: inherit;
}

/* Color picker */
.color-field { display: flex; gap: 8px; align-items: center; }
.color-swatch-wrap {
  width: 38px; height: 38px; border-radius: 9px; flex-shrink: 0;
  border: 1px solid #e2e8f0; cursor: pointer; overflow: hidden; position: relative;
}
.color-native {
  position: absolute; inset: 0; opacity: 0; cursor: pointer;
  width: 100%; height: 100%; border: none; padding: 0;
}
.color-hex { flex: 1; }

/* Receipt mode pills */
.mode-pills { display: flex; align-items: center; gap: 6px; flex-wrap: wrap; }
.mode-pill {
  display: flex; align-items: center; gap: 5px; padding: 6px 14px;
  border-radius: 999px; border: 1px solid #e2e8f0; background: #f8fafc;
  color: #475569; font-family: 'Outfit', sans-serif; font-size: 0.75rem; font-weight: 600;
  cursor: pointer; transition: all 0.18s;
}
.mode-pill--active { color: #fff; }

/* Subdomain */
.subdomain-box {
  border: 1.5px solid #22c55e; border-radius: 12px; padding: 14px 16px;
  background: rgba(34,197,94,0.04); display: flex; flex-direction: column; gap: 10px;
}
.subdomain-title-row { display: flex; align-items: center; gap: 8px; }
.subdomain-name { font-size: 0.85rem; font-weight: 700; color: #0f172a; }
.subdomain-free {
  font-size: 0.65rem; font-weight: 700; color: #22c55e;
  background: rgba(34,197,94,0.12); border: 1px solid rgba(34,197,94,0.25);
  padding: 2px 8px; border-radius: 999px;
}
.subdomain-hint { font-size: 0.75rem; color: #64748b; margin: 0; }
.subdomain-hint .hl { color: #22c55e; font-weight: 500; }
.slug-row    { display: flex; gap: 8px; }
.slug-input  { flex: 1; }
.slug-domain { width: 140px; padding: 9px 10px; border-radius: 9px; border: 1px solid #e2e8f0; background: #fff; color: #0f172a; font-family: 'Outfit', sans-serif; font-size: 0.82rem; cursor: pointer; flex-shrink: 0; }
.slug-domain:focus { outline: none; border-color: #22c55e; }
.slug-note { font-size: 0.72rem; color: #64748b; margin: 0; }
.slug-note .hl { color: #22c55e; font-weight: 500; }

/* Toggle */
.toggle-row    { display: flex; align-items: flex-start; justify-content: space-between; gap: 16px; }
.toggle-info   { display: flex; flex-direction: column; gap: 3px; flex: 1; }
.toggle-label-row { display: flex; align-items: center; gap: 6px; }
.toggle-switch {
  width: 44px; height: 24px; border-radius: 12px; border: none;
  background: #e2e8f0; position: relative; cursor: pointer; flex-shrink: 0;
  transition: background 0.2s; margin-top: 2px;
}
.toggle-switch::after {
  content: ''; position: absolute; width: 18px; height: 18px;
  border-radius: 50%; background: #fff; top: 3px; left: 3px;
  transition: transform 0.2s; box-shadow: 0 1px 3px rgba(0,0,0,0.2);
}
.toggle-switch--on::after { transform: translateX(20px); }

/* Dynamic list */
.list-header  { display: flex; align-items: center; justify-content: space-between; }
.dynamic-list { display: flex; flex-direction: column; gap: 8px; }
.dynamic-row  { display: flex; gap: 8px; align-items: center; }
.remove-btn {
  width: 30px; height: 30px; flex-shrink: 0; border-radius: 8px;
  border: 1px solid #e2e8f0; background: #fff; color: #94a3b8;
  display: flex; align-items: center; justify-content: center; cursor: pointer;
  transition: background 0.18s, color 0.18s;
}
.remove-btn:hover { background: #fee2e2; color: #ef4444; border-color: #fca5a5; }
.add-btn {
  font-size: 0.75rem; font-weight: 700; color: #22c55e; background: rgba(34,197,94,0.1);
  border: 1px solid rgba(34,197,94,0.25); border-radius: 8px; padding: 5px 12px;
  cursor: pointer; font-family: 'Outfit', sans-serif; transition: background 0.18s; white-space: nowrap;
}
.add-btn:hover { background: rgba(34,197,94,0.18); }

/* Error */
.form-error {
  font-size: 0.78rem; color: #ef4444; background: rgba(239,68,68,0.08);
  border: 1px solid rgba(239,68,68,0.2); border-radius: 8px; padding: 10px 14px; margin: 0;
}

/* Generate button */
.generate-btn {
  width: 100%; padding: 15px; border-radius: 12px; border: none; color: #fff;
  font-family: 'Outfit', sans-serif; font-size: 0.92rem; font-weight: 700;
  cursor: pointer; transition: filter 0.2s, transform 0.18s;
  display: flex; align-items: center; justify-content: center; gap: 8px;
}
.generate-btn:hover:not(.generate-btn--loading) { filter: brightness(1.1); transform: translateY(-1px); }
.generate-btn--loading { opacity: 0.75; cursor: not-allowed; }
.spinner {
  width: 14px; height: 14px; border-radius: 50%;
  border: 2px solid rgba(255,255,255,0.35); border-top-color: #fff;
  animation: spin 0.7s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }

/* My receipts */
.my-receipts-card {
  background: #fff; border: 1px solid #e8ecf0; border-radius: 14px;
  padding: 18px 22px; display: flex; flex-direction: column; gap: 12px;
  box-shadow: 0 1px 6px rgba(0,0,0,0.05);
}
.my-receipts-title  { font-size: 0.9rem; font-weight: 700; color: #0f172a; margin: 0; }
.my-receipts-empty p { font-size: 0.8rem; color: #94a3b8; margin: 0; }
.my-receipts-empty .hl { color: #22c55e; }
.receipts-list { display: flex; flex-direction: column; gap: 8px; }
.receipt-item {
  display: flex; align-items: center; gap: 10px; padding: 10px 12px;
  border-radius: 10px; background: #f8fafc; border: 1px solid #e8ecf0;
}
.receipt-item__dot  { width: 10px; height: 10px; border-radius: 50%; flex-shrink: 0; }
.receipt-item__info { flex: 1; display: flex; flex-direction: column; gap: 2px; min-width: 0; }
.receipt-item__name { font-size: 0.82rem; font-weight: 600; color: #0f172a; }
.receipt-item__meta { font-size: 0.7rem; color: #94a3b8; }
.receipt-item__badge { font-size: 0.65rem; font-weight: 700; padding: 2px 8px; border-radius: 999px; border: 1px solid; white-space: nowrap; }

/* ── Preview column ── */
.pt-preview-col {
  position: sticky; top: 24px;
  display: flex; flex-direction: column; gap: 10px;
}
.preview-label {
  font-size: 0.68rem; font-weight: 700; text-transform: uppercase;
  letter-spacing: 0.1em; color: var(--t3); margin: 0;
}

/* Receipt card */
.receipt-preview {
  border-radius: 22px; padding: 28px 22px 20px;
  display: flex; flex-direction: column; align-items: center; gap: 14px;
  box-shadow: 0 24px 64px rgba(0,0,0,0.4);
}

/* Check circle */
.rp-check-circle {
  width: 60px; height: 60px; border-radius: 50%; border: 2px solid;
  display: flex; align-items: center; justify-content: center; overflow: hidden;
}
.rp-logo-img { width: 100%; height: 100%; object-fit: contain; }

/* Service name */
.rp-service-name {
  font-size: 1.1rem; font-weight: 900; color: #fff; letter-spacing: 0.05em;
  text-align: center; line-height: 1.3; margin: 0;
}

/* Progress spinner */
.rp-progress-row {
  display: flex; align-items: center; gap: 14px; width: 100%;
  justify-content: center;
}
.rp-spinner-wrap  { position: relative; width: 64px; height: 64px; flex-shrink: 0; }
.rp-spin-svg      { position: absolute; inset: 0; animation: rotate 2.8s linear infinite; transform-origin: 32px 32px; }
@keyframes rotate { to { transform: rotate(360deg); } }
.rp-initial {
  position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);
  width: 38px; height: 38px; border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
  font-weight: 900; font-size: 1rem; color: #fff;
}
.rp-status-text { font-size: 1rem; font-weight: 700; }

/* Progress bar */
.rp-progress-bar-wrap  { display: flex; align-items: center; gap: 8px; width: 100%; }
.rp-progress-bar-track {
  flex: 1; height: 5px; border-radius: 999px; background: rgba(255,255,255,0.1); overflow: hidden;
}
.rp-progress-bar-fill  { height: 100%; border-radius: 999px; transition: width 0.4s ease; }
.rp-progress-pct       { font-size: 0.72rem; font-weight: 700; }

/* Divider */
.rp-divider { width: 100%; border: none; border-top: 1px solid rgba(255,255,255,0.08); }

/* Meta rows */
.rp-meta-row  { display: flex; align-items: center; justify-content: space-between; width: 100%; }
.rp-meta-label { font-size: 0.78rem; color: rgba(255,255,255,0.45); }
.rp-meta-value { font-size: 0.78rem; color: rgba(255,255,255,0.8); font-weight: 600; }
.rp-status-badge {
  font-size: 0.65rem; font-weight: 800; color: #fff; padding: 3px 10px; border-radius: 999px;
  letter-spacing: 0.05em;
}

/* Live chat */
.rp-chat-btn {
  width: 100%; padding: 11px; border-radius: 999px; border: none;
  color: #fff; font-family: 'Outfit', sans-serif; font-size: 0.82rem; font-weight: 700;
  cursor: default; display: flex; align-items: center; justify-content: center; gap: 8px;
}

/* Footer */
.rp-footer-row  { display: flex; align-items: center; justify-content: space-between; width: 100%; }
.rp-footer-text { font-size: 0.68rem; color: rgba(255,255,255,0.3); }
.rp-thank-you   { font-size: 0.68rem; font-weight: 700; }

/* Toast */
.success-toast {
  position: fixed; bottom: 28px; right: 28px; z-index: 400;
  display: flex; align-items: center; gap: 10px; padding: 14px 20px; border-radius: 14px;
  color: #fff; font-family: 'Outfit', sans-serif;
  font-size: 0.85rem; font-weight: 600; box-shadow: 0 8px 28px rgba(0,0,0,0.3);
}
.toast-pop-enter-active { transition: opacity 0.3s ease, transform 0.3s cubic-bezier(0.34,1.56,0.64,1); }
.toast-pop-leave-active { transition: opacity 0.2s ease, transform 0.2s ease; }
.toast-pop-enter-from,
.toast-pop-leave-to     { opacity: 0; transform: translateY(12px) scale(0.95); }

/* Responsive */
@media (max-width: 1100px) { .pt-layout { grid-template-columns: 1fr 300px; } }
@media (max-width: 900px) {
  .pt-layout { grid-template-columns: 1fr; }
  .pt-preview-col { position: static; order: -1; }
}
@media (max-width: 600px) {
  .two-col { grid-template-columns: 1fr; }
  .slug-row { flex-direction: column; }
  .slug-domain { width: 100%; }
  .logo-row { flex-wrap: wrap; }
}
</style>
