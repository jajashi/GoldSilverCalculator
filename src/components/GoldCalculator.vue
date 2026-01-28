<template>
  <div class="calculator-container calculator-container--gold">
    <div class="calculator-card">
      <div class="calculator-header">
        <div>
          <h1 class="title">Gold Price Calculator</h1>
          <p class="subtitle">
            Professional pricing tool for 24K–18K gold, including design charge and PH tax.
          </p>
        </div>
        <div class="calculator-seal calculator-seal--gold">
          <span class="seal-main">Gold</span>
          <span class="seal-sub">24K • 23K • 22K</span>
        </div>
      </div>

      <div class="calculator-form">
        <div class="form-group">
          <label>Purity Level</label>
          <select v-model="purity" class="form-control">
            <option v-for="p in goldPurityLevels" :key="p.value" :value="p.value">
              {{ p.label }} ({{ p.percentage }}% pure)
            </option>
          </select>
        </div>

        <div class="form-group">
          <label>Rate per Gram (₱)</label>
          <input type="number" class="form-control" :value="rate" readonly />
        </div>

        <div class="form-group">S
          <label>Weight (Grams)</label>
          <input type="number" class="form-control" v-model.number="grams" />
        </div>

        <div class="form-group">
          <label>Design Charge (₱)</label>
          <input type="number" class="form-control" v-model.number="designCharge" />
        </div>

        <div class="calculation-breakdown" v-if="total > 0">
          <div class="breakdown-header">Price breakdown summary</div>

          <div class="breakdown-item">
            <span>Base Cost</span>
            <span>₱{{ format(baseCost) }}</span>
          </div>
          <div class="breakdown-item">
            <span>Design Charge</span>
            <span>₱{{ format(designCharge) }}</span>
          </div>
          <div class="breakdown-item">
            <span>PH Tax (12%)</span>
            <span>₱{{ format(tax) }}</span>
          </div>
          <div class="breakdown-item">
            <span>Total Price</span>
            <span class="highlight">₱{{ format(total) }}</span>
          </div>
        </div>

        <button class="reset-btn" v-if="total > 0" @click="reset">
          Reset calculator
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const purity = ref('24K')
const grams = ref(0)
const designCharge = ref(0)
const rate = ref(9942.46)

const goldPurityLevels = [
  { value: '24K', label: '24K Gold', percentage: 99.9, ratePerGram: 9942.46 },
  { value: '23K', label: '23K Gold', percentage: 95.8, ratePerGram: 9528.19 },
  { value: '22K', label: '22K Gold', percentage: 91.6, ratePerGram: 9113.92 },
  { value: '21K', label: '21K Gold', percentage: 87.5, ratePerGram: 8699.65 },
  { value: '18K', label: '18K Gold', percentage: 75, ratePerGram: 7456.85 }
]

watch(purity, () => {
  const selected = goldPurityLevels.find(p => p.value === purity.value)
  if (selected) rate.value = selected.ratePerGram
})

const baseCost = computed(() => rate.value * grams.value)
const subtotal = computed(() => baseCost.value + designCharge.value)
const tax = computed(() => subtotal.value * 0.12)
const total = computed(() => subtotal.value + tax.value)

const format = v =>
  new Intl.NumberFormat('en-PH', { minimumFractionDigits: 2 }).format(v)

const reset = () => {
  grams.value = 0
  designCharge.value = 0
  purity.value = '24K'
  rate.value = 9942.46
}
</script>

<style scoped>
.calculator-container {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 1.75rem 1rem 3rem;
 
  min-height: auto;
  background: radial-gradient(circle at top left, rgba(250, 204, 21, 0.12), transparent 60%);
}

.calculator-card {
  position: relative;
  background: radial-gradient(circle at top, #020617, #020617) padding-box,
    linear-gradient(135deg, rgba(250, 204, 21, 0.9), rgba(244, 114, 182, 0.9))
      border-box;
  border-radius: 26px;
  box-shadow:
    0 28px 70px rgba(15, 23, 42, 0.95),
    0 0 0 1px rgba(234, 179, 8, 0.55);
  padding: 2rem 2rem 2.25rem;
  max-width: 620px;
  width: 100%;
  border: 1.5px solid transparent;
  overflow: hidden;
}

.calculator-container--gold .calculator-card::before {
  content: '';
  position: absolute;
  inset: -40%;
  background:
    radial-gradient(circle at 10% 0%, rgba(250, 204, 21, 0.16), transparent 60%),
    radial-gradient(circle at 90% 0%, rgba(249, 115, 22, 0.15), transparent 65%);
  opacity: 0.9;
  pointer-events: none;
}

.calculator-card > * {
  position: relative;
  z-index: 1;
}

.calculator-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 1.5rem;
  margin-bottom: 1.75rem;
}

.title {
  color: #fefce8;
  font-size: 1.7rem;
  font-weight: 800;
  letter-spacing: 0.03em;
}

.subtitle {
  color: #e5e7eb;
  font-size: 0.9rem;
  margin-top: 0.35rem;
  max-width: 360px;
}

.calculator-seal {
  display: inline-flex;
  flex-direction: column;
  align-items: flex-end;
  padding: 0.6rem 0.85rem;
  border-radius: 999px;
  background: radial-gradient(circle at top left, #facc15, #f97316);
  color: #111827;
  box-shadow: 0 16px 35px rgba(15, 23, 42, 0.9);
  min-width: 120px;
}

.calculator-seal--gold .seal-main {
  font-size: 0.8rem;
}

.seal-main {
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  font-size: 0.78rem;
}

.seal-sub {
  font-size: 0.7rem;
  opacity: 0.9;
}

.calculator-form {
  display: grid;
  grid-template-columns: minmax(0, 1fr) minmax(0, 1fr);
  gap: 1rem 1.25rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.45rem;
}

.form-group label {
  font-size: 0.85rem;
  font-weight: 600;
  color: #e5e7eb;
}

.form-control {
  width: 100%;
  padding: 0.8rem 1rem;
  border-radius: 12px;
  border: 1px solid rgba(148, 163, 184, 0.7);
  background: radial-gradient(circle at top, rgba(15, 23, 42, 0.96), rgba(15, 23, 42, 1));
  color: #f9fafb;
  font-size: 0.95rem;
}

.form-control::placeholder {
  color: #6b7280;
}

.form-control:focus {
  outline: none;
  border-color: #facc15;
  box-shadow:
    0 0 0 1px rgba(250, 204, 21, 0.7),
    0 0 0 6px rgba(234, 179, 8, 0.2);
}

.calculation-breakdown {
  grid-column: 1 / -1;
  margin-top: 0.75rem;
  padding: 1.4rem 1.25rem 1.25rem;
  background: radial-gradient(circle at top, rgba(15, 23, 42, 0.95), rgba(15, 23, 42, 1));
  border-radius: 18px;
  border: 1px solid rgba(234, 179, 8, 0.65);
  box-shadow: 0 16px 38px rgba(15, 23, 42, 0.85);
}

.breakdown-header {
  font-size: 0.8rem;
  text-transform: uppercase;
  letter-spacing: 0.16em;
  color: #facc15;
  margin-bottom: 0.7rem;
}

.breakdown-item {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  padding: 0.35rem 0;
  font-size: 0.9rem;
  color: #e5e7eb;
}

.breakdown-item span:last-child {
  font-variant-numeric: tabular-nums;
}

.highlight {
  color: #fef9c3;
  font-weight: 800;
  font-size: 1.2rem;
}

.reset-btn {
  grid-column: 1 / -1;
  margin-top: 0.85rem;
  padding: 0.9rem 1.2rem;
  width: 100%;
  border-radius: 999px;
  border: none;
  background: radial-gradient(circle at top left, #111827, #020617);
  color: #e5e7eb;
  font-weight: 600;
  font-size: 0.9rem;
  cursor: pointer;
  box-shadow: 0 18px 40px rgba(15, 23, 42, 0.95);
  transition: transform 0.16s ease, box-shadow 0.16s ease, background 0.16s ease;
}

.reset-btn:hover {
  transform: translateY(-1px);
  background: radial-gradient(circle at top left, #020617, #020617);
  box-shadow: 0 22px 55px rgba(15, 23, 42, 0.98);
}

@media (max-width: 600px) {
  .calculator-card {
    padding: 1.5rem 1.35rem 1.7rem;
    border-radius: 22px;
  }

  .calculator-header {
    flex-direction: column;
    align-items: flex-start;
  }

  .calculator-seal {
    align-self: flex-start;
  }

  .calculator-form {
    grid-template-columns: minmax(0, 1fr);
  }
}
</style>