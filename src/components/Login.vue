<template>
  <div class="auth-container">
    <div class="auth-card">
      <h1 class="auth-title">
        <span class="title-icon">Login</span>
      </h1>
      <p class="auth-subtitle">
        Sign in to access the official gold &amp; silver pricing console.
      </p>
      
      <form @submit.prevent="handleLogin" class="auth-form">
        <div class="form-group">
          <label for="email">Email</label>
          <input
            id="email"
            type="email"
            v-model="email"
            placeholder="Enter your email"
            class="form-control"
            required
          />
        </div>

        <div class="form-group">
          <label for="password">Password</label>
          <input
            id="password"
            type="password"
            v-model="password"
            placeholder="Enter your password"
            class="form-control"
            required
          />
        </div>

        <div v-if="error" class="error-message">
          {{ error }}
        </div>

        <button type="submit" class="auth-btn" :disabled="loading">
          <span v-if="loading">Logging in...</span>
          <span v-else>Login</span>
        </button>

        <div class="auth-footer">
          <p>
            Don't have an account?
            <a href="#" @click.prevent="$emit('switchToSignup')">Create an account</a>
          </p>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['login', 'switchToSignup'])

const email = ref('')
const password = ref('')
const error = ref('')
const loading = ref(false)

const handleLogin = async () => {
  error.value = ''
  loading.value = true

  // Simulate API call
  setTimeout(() => {
    // Get users from localStorage
    const users = JSON.parse(localStorage.getItem('users') || '[]')
    const user = users.find(u => u.email === email.value && u.password === password.value)

    if (user) {
      // Store current user session
      localStorage.setItem('currentUser', JSON.stringify({ email: user.email, name: user.name }))
      emit('login', { email: user.email, name: user.name })
    } else {
      error.value = 'Invalid email or password'
    }
    loading.value = false
  }, 500)
}
</script>

<style scoped>
.auth-container {
  display: flex;
  justify-content: center;
  align-items: stretch;
  padding: 0;
  min-height: auto;
  background: transparent;
}

.auth-card {
  background: radial-gradient(circle at top, rgba(15, 23, 42, 0.98), #020617);
  border-radius: 22px;
  box-shadow: 0 18px 45px rgba(15, 23, 42, 0.95);
  padding: 1.8rem 1.6rem;
  max-width: 420px;
  width: 100%;
  border: 1px solid rgba(148, 163, 184, 0.7);
}

.auth-title {
  color: #f9fafb;
  font-size: 1.4rem;
  font-weight: 800;
  margin-bottom: 0.5rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.title-icon {
  font-size: 0.85rem;
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: #e5e7eb;
}

.auth-subtitle {
  color: #9ca3af;
  font-size: 0.9rem;
  margin-bottom: 1.6rem;
  font-weight: 400;
}

.auth-form {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.form-group label {
  color: #e5e7eb;
  font-weight: 600;
  font-size: 0.95rem;
}

.form-control {
  padding: 0.875rem 1.25rem;
  border: 1px solid rgba(148, 163, 184, 0.75);
  border-radius: 999px;
  font-size: 1rem;
  background: radial-gradient(circle at top, rgba(15, 23, 42, 0.96), rgba(15, 23, 42, 1));
  color: #f9fafb;
}

.form-control:focus {
  outline: none;
  border-color: #facc15;
  box-shadow:
    0 0 0 1px rgba(250, 204, 21, 0.7),
    0 0 0 6px rgba(234, 179, 8, 0.22);
}

.error-message {
  padding: 0.75rem 1rem;
  background: rgba(239, 68, 68, 0.1);
  border: 1px solid rgba(239, 68, 68, 0.3);
  border-radius: 8px;
  color: #dc2626;
  font-size: 0.9rem;
  text-align: center;
}

.auth-btn {
  padding: 1rem 2rem;
  background: radial-gradient(circle at top left, #facc15, #f97316);
  color: white;
  border: none;
  border-radius: 999px;
  font-size: 1.05rem;
  font-weight: 700;
  cursor: pointer;
  box-shadow: 0 14px 30px rgba(15, 23, 42, 0.85);
  transition: background 0.3s, box-shadow 0.3s;
}

.auth-btn:hover:not(:disabled) {
  box-shadow: 0 18px 40px rgba(15, 23, 42, 0.95);
}

.auth-btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.auth-footer {
  text-align: center;
  margin-top: 1rem;
}

.auth-footer p {
  color: #9ca3af;
  font-size: 0.9rem;
}

.auth-footer a {
  color: #facc15;
  font-weight: 600;
  text-decoration: none;
}

.auth-footer a:hover {
  color: #fbbf24;
  text-decoration: underline;
}

@media (max-width: 600px) {
  .auth-card {
    padding: 1.5rem 1.35rem;
  }

  .auth-title {
    font-size: 1.25rem;
  }
}
</style>
