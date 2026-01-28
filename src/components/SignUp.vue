<template>
  <div class="auth-container">
    <div class="auth-card">
      <h1 class="auth-title">
        <span class="title-icon">Registration</span>
      </h1>
      <p class="auth-subtitle">
        Create an operator profile to start using the gold &amp; silver console.
      </p>
      
      <form @submit.prevent="handleSignup" class="auth-form">
        <div class="form-group">
          <label for="name">Full Name</label>
          <input
            id="name"
            type="text"
            v-model="name"
            placeholder="Enter your full name"
            class="form-control"
            required
          />
        </div>

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
            placeholder="Create a password"
            class="form-control"
            required
            minlength="6"
          />
        </div>

        <div class="form-group">
          <label for="confirmPassword">Confirm Password</label>
          <input
            id="confirmPassword"
            type="password"
            v-model="confirmPassword"
            placeholder="Confirm your password"
            class="form-control"
            required
          />
        </div>

        <div v-if="error" class="error-message">
          {{ error }}
        </div>

        <div v-if="success" class="success-message">
          {{ success }}
        </div>

        <button type="submit" class="auth-btn" :disabled="loading">
          <span v-if="loading">Creating account...</span>
          <span v-else>Sign Up</span>
        </button>

        <div class="auth-footer">
          <p>Already have an account? <a href="#" @click.prevent="$emit('switchToLogin')">Login</a></p>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['signup', 'switchToLogin'])

const name = ref('')
const email = ref('')
const password = ref('')
const confirmPassword = ref('')
const error = ref('')
const success = ref('')
const loading = ref(false)

const handleSignup = async () => {
  error.value = ''
  success.value = ''
  loading.value = true

  // Validate passwords match
  if (password.value !== confirmPassword.value) {
    error.value = 'Passwords do not match'
    loading.value = false
    return
  }

  // Validate password length
  if (password.value.length < 6) {
    error.value = 'Password must be at least 6 characters'
    loading.value = false
    return
  }

  // Simulate API call
  setTimeout(() => {
    // Get existing users from localStorage
    const users = JSON.parse(localStorage.getItem('users') || '[]')
    
    // Check if email already exists
    if (users.find(u => u.email === email.value)) {
      error.value = 'Email already registered'
      loading.value = false
      return
    }

    // Create new user
    const newUser = {
      name: name.value,
      email: email.value,
      password: password.value
    }

    users.push(newUser)
    localStorage.setItem('users', JSON.stringify(users))

    // Store current user session
    localStorage.setItem('currentUser', JSON.stringify({ email: newUser.email, name: newUser.name }))
    
    success.value = 'Account created successfully!'
    
    setTimeout(() => {
      emit('signup', { email: newUser.email, name: newUser.name })
    }, 1000)
  }, 500)
}
</script>

<style scoped>
.auth-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: auto;
  padding: 0;
  background: transparent;
}

.auth-card {
  background: radial-gradient(circle at top, rgba(15, 23, 42, 0.98), #020617);
  border-radius: 22px;
  box-shadow: 0 18px 45px rgba(15, 23, 42, 0.95);
  padding: 1.8rem 1.6rem;
  max-width: 450px;
  width: 100%;
  border: 1px solid rgba(148, 163, 184, 0.7);
}

.auth-title {
  color: #f9fafb;
  font-size: 1.4rem;
  font-weight: 800;
  margin-bottom: 0.5rem;
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: center;
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
  text-align: center;
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
  background: radial-gradient(circle at top, rgba(15, 23, 42, 0.98), rgba(15, 23, 42, 1));
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

.success-message {
  padding: 0.75rem 1rem;
  background: rgba(34, 197, 94, 0.12);
  border: 1px solid rgba(22, 163, 74, 0.7);
  border-radius: 8px;
  color: #bbf7d0;
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
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.auth-btn:hover:not(:disabled) {
  transform: translateY(-2px);
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

  .auth-container {
    padding: 1rem;
  }
}
</style>