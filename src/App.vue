<script setup>
import { ref, onMounted } from 'vue'

import GoldCalculator from './components/GoldCalculator.vue'
import SilverCalculator from './components/SilverCalculator.vue'
import Login from './components/Login.vue'
import SignUp from './components/SignUp.vue'
import LogoMetallic from './assets/logo-metallic.svg'
import CoinsHero from './assets/coins-hero.svg'

const isAuthenticated = ref(false)
const showSignUp = ref(false)
const currentUser = ref(null)

// page state: 'gold' | 'silver'
const activeCalculator = ref('gold')

// Restore session
onMounted(() => {
  const user = localStorage.getItem('currentUser')
  if (user) {
    currentUser.value = JSON.parse(user)
    isAuthenticated.value = true
  }
})

const handleLogin = (user) => {
  currentUser.value = user
  isAuthenticated.value = true
  localStorage.setItem('currentUser', JSON.stringify(user))
}

const handleSignup = (user) => {
  currentUser.value = user
  isAuthenticated.value = true
  localStorage.setItem('currentUser', JSON.stringify(user))
}

const handleLogout = () => {
  localStorage.removeItem('currentUser')
  currentUser.value = null
  isAuthenticated.value = false
  showSignUp.value = false
  activeCalculator.value = 'gold'
}

const switchToSignup = () => {
  showSignUp.value = true
}

const switchToLogin = () => {
  showSignUp.value = false
}
</script>

<template>
  <!-- AUTH -->
  <div v-if="!isAuthenticated" class="shell-auth">
    <div class="shell-auth-inner">
      <header class="shell-auth-header">
        <div class="brand">
          <img :src="LogoMetallic" alt="Precious Metals logo" class="brand-logo" />
          <div class="brand-copy">
            <h1 class="brand-name">Precious Metals Desk</h1>
            <p class="brand-tagline">
              Official Gold &amp; Silver Price Calculator
            </p>
          </div>
        </div>
        <p class="brand-helper">
          Secure calculator experience for jewelers, traders, and serious collectors.
        </p>
      </header>

      <main class="shell-auth-main">
        <section class="shell-auth-panel">
          <Login
            v-if="!showSignUp"
            @login="handleLogin"
            @switch-to-signup="switchToSignup"
          />
          <SignUp
            v-else
            @signup="handleSignup"
            @switch-to-login="switchToLogin"
          />
        </section>

        <aside class="shell-auth-aside">
          <div class="aside-hero" :style="{ backgroundImage: `url(${CoinsHero})` }" aria-hidden="true" />
          <h2>Why traders use this tool</h2>
          <ul>
            <li>Consistent pricing for gold and silver pieces</li>
            <li>Transparent breakdown of base cost, design, and tax</li>
            <li>Fast, offline-friendly calculation for store counters</li>
          </ul>
          <div class="shell-auth-badge">
            <span class="badge-pill">24K · 23K · 22K · Sterling</span>
          </div>
        </aside>
      </main>
    </div>
  </div>

  <!-- APP -->
  <div v-else class="shell-app">
    <!-- HEADER -->
    <header class="header">
      <div class="header-inner">
        <div class="brand">
          <img :src="LogoMetallic" alt="Precious Metals logo" class="brand-logo" />
          <div class="brand-copy">
            <h1 class="brand-name">Precious Metals Desk</h1>
            <p class="brand-tagline">
              Institutional Gold &amp; Silver Calculator
            </p>
          </div>
        </div>

        <div class="header-meta">
          <span class="user-pill">
            Signed in as
            <strong>{{ currentUser?.name }}</strong>
          </span>
          <button class="logout-btn" @click="handleLogout">
            Logout
          </button>
        </div>
      </div>

      <!-- NAV + CONTEXT -->
      <div class="header-lower">
        <div class="nav-buttons">
          <button
            :class="{ active: activeCalculator === 'gold' }"
            @click="activeCalculator = 'gold'"
          >
            Gold Calculator
          </button>

          <button
            :class="{ active: activeCalculator === 'silver' }"
            @click="activeCalculator = 'silver'"
          >
            Silver Calculator
          </button>
        </div>

        <p class="header-caption">
          Enter purity, market rate, and design charge to obtain a compliant price
          including 12% PH tax.
        </p>
      </div>
    </header>

    <!-- CONTENT -->
    <main class="content">
      <GoldCalculator v-if="activeCalculator === 'gold'" />
      <SilverCalculator v-if="activeCalculator === 'silver'" />
    </main>

    <footer class="site-footer">
      <p>For internal pricing use only. Always verify against your latest rate sheet.</p>
    </footer>
  </div>
</template>

<style>
.shell-app {
  color: #0b1120;
}

/* HEADER SHELL */
.header {
  position: fixed;
  inset: 0 0 auto;
  padding: 0.75rem 1.25rem 1.25rem;
  backdrop-filter: blur(16px);
  background:
    linear-gradient(135deg, rgba(15, 23, 42, 0.88), rgba(30, 64, 175, 0.7)),
    radial-gradient(circle at top left, rgba(250, 204, 21, 0.35), transparent 60%);
  box-shadow: 0 18px 45px rgba(15, 23, 42, 0.65);
  z-index: 1000;
  border-bottom: 1px solid rgba(148, 163, 184, 0.5);
}

.header-inner {
  max-width: 1120px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1.5rem;
}

.brand {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.brand-logo {
  height: 40px;
  width: auto;
  filter: drop-shadow(0 4px 10px rgba(15, 23, 42, 0.6));
}

.brand-copy {
  display: flex;
  flex-direction: column;
}

.brand-name {
  font-size: 1.05rem;
  font-weight: 700;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  color: #f9fafb;
}

.brand-tagline {
  font-size: 0.8rem;
  color: #e5e7eb;
}

.header-meta {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.user-pill {
  font-size: 0.85rem;
  padding: 0.25rem 0.75rem;
  border-radius: 999px;
  border: 1px solid rgba(148, 163, 184, 0.7);
  color: #e5e7eb;
}

.user-pill strong {
  margin-left: 0.25rem;
  color: #fef9c3;
}

.logout-btn {
  padding: 0.45rem 1.2rem;
  background: #fef9c3;
  color: #854d0e;
  border-radius: 999px;
  border: none;
  font-weight: 600;
  cursor: pointer;
  font-size: 0.85rem;
  box-shadow: 0 10px 25px rgba(15, 23, 42, 0.6);
  transition: transform 0.16s ease, box-shadow 0.16s ease, background 0.16s ease;
}

.logout-btn:hover {
  transform: translateY(-1px);
  background: #facc15;
  box-shadow: 0 14px 35px rgba(15, 23, 42, 0.75);
}

.header-lower {
  max-width: 1120px;
  margin: 0.6rem auto 0;
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 1.25rem;
}

.nav-buttons {
  display: inline-flex;
  padding: 0.22rem;
  border-radius: 999px;
  background: rgba(15, 23, 42, 0.7);
  box-shadow: inset 0 0 0 1px rgba(148, 163, 184, 0.6);
}

.nav-buttons button {
  padding: 0.5rem 1.4rem;
  border-radius: 999px;
  border: none;
  background: transparent;
  color: #e5e7eb;
  font-weight: 600;
  font-size: 0.85rem;
  cursor: pointer;
  transition: background 0.2s ease, color 0.2s ease, transform 0.1s ease;
}

.nav-buttons button:hover {
  transform: translateY(-1px);
}

.nav-buttons button.active {
  background: linear-gradient(135deg, #facc15, #f97316);
  color: #111827;
  box-shadow: 0 10px 22px rgba(234, 179, 8, 0.55);
}

.header-caption {
  font-size: 0.78rem;
  max-width: 420px;
  color: #cbd5f5;
  text-align: right;
}

.content {
  padding-top: 155px; /* space for fixed header */
  max-width: 960px;
  margin: 0 auto;
  padding-left: 1rem;
  padding-right: 1rem;
}

.site-footer {
  max-width: 960px;
  margin: 1.5rem auto 1.75rem;
  padding: 0 1rem;
  font-size: 0.78rem;
  color: #9ca3af;
}

.site-footer p {
  padding-top: 0.75rem;
  border-top: 1px dashed rgba(148, 163, 184, 0.6);
}

/* AUTH SHELL */
.shell-auth {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1.75rem 1.25rem;
}

.shell-auth-inner {
  width: 100%;
  max-width: 1040px;
  border-radius: 28px;
  padding: 1.75rem 1.75rem 1.5rem;
  background:
    radial-gradient(circle at top left, rgba(250, 204, 21, 0.22), transparent 60%),
    radial-gradient(circle at bottom right, rgba(56, 189, 248, 0.25), transparent 60%),
    rgba(15, 23, 42, 0.92);
  box-shadow:
    0 24px 55px rgba(15, 23, 42, 0.85),
    0 0 0 1px rgba(148, 163, 184, 0.4);
  color: #e5e7eb;
}

.shell-auth-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1.5rem;
  padding-bottom: 1.25rem;
  border-bottom: 1px solid rgba(148, 163, 184, 0.45);
  margin-bottom: 1.25rem;
}

.brand-helper {
  max-width: 320px;
  font-size: 0.85rem;
  color: #cbd5f5;
}

.shell-auth-main {
  display: grid;
  grid-template-columns: minmax(0, 1.3fr) minmax(0, 1fr);
  gap: 1.75rem;
  align-items: stretch;
}

.shell-auth-panel {
  background: radial-gradient(circle at top left, rgba(15, 23, 42, 0.85), rgba(15, 23, 42, 0.98));
  border-radius: 24px;
  padding: 1.25rem;
  box-shadow: inset 0 0 0 1px rgba(148, 163, 184, 0.3);
}

.shell-auth-aside {
  background: radial-gradient(circle at top, rgba(15, 23, 42, 0.96), rgba(15, 23, 42, 1));
  border-radius: 24px;
  padding: 1.5rem 1.4rem;
  box-shadow:
    inset 0 0 0 1px rgba(30, 64, 175, 0.7),
    0 18px 45px rgba(15, 23, 42, 0.85);
}

.aside-hero {
  width: 100%;
  height: 140px;
  border-radius: 18px;
  background-size: cover;
  background-position: center;
  box-shadow:
    inset 0 0 0 1px rgba(148, 163, 184, 0.35),
    0 16px 35px rgba(15, 23, 42, 0.9);
  margin-bottom: 1.1rem;
  opacity: 0.95;
}

.shell-auth-aside h2 {
  font-size: 1rem;
  font-weight: 600;
  margin-bottom: 0.75rem;
  color: #e5e7eb;
}

.shell-auth-aside ul {
  list-style: none;
  padding-left: 0;
  margin: 0 0 1.25rem;
  font-size: 0.86rem;
}

.shell-auth-aside li {
  position: relative;
  padding-left: 1.1rem;
  color: #cbd5f5;
  margin-bottom: 0.35rem;
}

.shell-auth-aside li::before {
  content: '';
  position: absolute;
  left: 0.2rem;
  top: 0.45rem;
  width: 0.35rem;
  height: 0.35rem;
  border-radius: 999px;
  background: radial-gradient(circle, #facc15, #f97316);
  box-shadow: 0 0 0 3px rgba(251, 191, 36, 0.3);
}

.shell-auth-badge {
  display: flex;
  justify-content: flex-start;
}

.badge-pill {
  font-size: 0.75rem;
  padding: 0.3rem 0.8rem;
  border-radius: 999px;
  background: radial-gradient(circle at top left, #facc15, #f97316);
  color: #111827;
  font-weight: 600;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  box-shadow: 0 10px 22px rgba(15, 23, 42, 0.85);
}

@media (max-width: 900px) {
  .header-inner {
    flex-direction: column;
    align-items: flex-start;
  }

  .header-meta {
    align-self: stretch;
    justify-content: space-between;
  }

  .header-lower {
    flex-direction: column;
    align-items: flex-start;
  }

  .header-caption {
    text-align: left;
    max-width: 100%;
  }

  .shell-auth-main {
    grid-template-columns: minmax(0, 1fr);
  }
}

@media (max-width: 640px) {
  .header {
    padding: 0.75rem 0.9rem 1.1rem;
  }

  .header-inner {
    gap: 0.75rem;
  }

  .brand-name {
    font-size: 0.95rem;
  }

  .brand-tagline {
    font-size: 0.75rem;
  }

  .shell-auth-inner {
    padding: 1.3rem 1.1rem 1.1rem;
    border-radius: 22px;
  }

  .shell-auth-header {
    flex-direction: column;
    align-items: flex-start;
  }

  .brand-helper {
    max-width: 100%;
  }
}
</style>