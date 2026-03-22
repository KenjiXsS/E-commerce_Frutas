<template>
  <div class="min-h-screen flex items-center justify-center px-6 py-16 relative">
    <!-- Background glow -->
    <div class="absolute inset-0 pointer-events-none overflow-hidden">
      <div class="absolute top-1/4 left-1/2 -translate-x-1/2 w-[600px] h-[400px] bg-emerald-500/6 rounded-full blur-3xl" />
    </div>

    <div class="w-full max-w-md relative animate-scale-in">
      <!-- Logo mark -->
      <div class="text-center mb-8">
        <div class="inline-flex items-center justify-center w-14 h-14 bg-emerald-500/10 border border-emerald-500/20 rounded-2xl mb-4 text-2xl">
          🍃
        </div>
        <h1 class="text-2xl font-bold text-white">Bem-vindo de volta</h1>
        <p class="text-slate-400 text-sm mt-1">Entre na sua conta para continuar</p>
      </div>

      <!-- Card -->
      <div class="card p-8">
        <!-- Error message -->
        <div v-if="error" class="mb-5 flex items-center gap-3 px-4 py-3 bg-red-500/10 border border-red-500/20 rounded-xl text-red-400 text-sm">
          <i class="fas fa-exclamation-circle flex-shrink-0"></i>
          {{ error }}
        </div>

        <form @submit.prevent="handleLogin" class="space-y-5">
          <div>
            <label class="label">E-mail</label>
            <input
              v-model="email"
              type="email"
              class="input"
              placeholder="seu@email.com"
              required
              autocomplete="email"
            />
          </div>

          <div>
            <div class="flex items-center justify-between mb-1.5">
              <label class="label mb-0">Senha</label>
              <a href="#" class="text-xs text-emerald-400 hover:text-emerald-300 transition-colors">
                Esqueci a senha
              </a>
            </div>
            <div class="relative">
              <input
                v-model="password"
                :type="showPassword ? 'text' : 'password'"
                class="input pr-11"
                placeholder="••••••••"
                required
                autocomplete="current-password"
              />
              <button
                type="button"
                @click="showPassword = !showPassword"
                class="absolute right-3 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-200 transition-colors"
              >
                <i :class="showPassword ? 'fas fa-eye-slash' : 'fas fa-eye'" class="text-sm"></i>
              </button>
            </div>
          </div>

          <div class="flex items-center gap-2">
            <input
              id="remember"
              v-model="rememberMe"
              type="checkbox"
              class="w-4 h-4 rounded border-slate-600 bg-surface-700 text-emerald-500 focus:ring-emerald-500 focus:ring-offset-0 focus:ring-offset-transparent"
            />
            <label for="remember" class="text-sm text-slate-400 cursor-pointer select-none">
              Lembrar de mim
            </label>
          </div>

          <button
            type="submit"
            class="btn btn-primary w-full py-3 text-sm"
            :disabled="loading"
          >
            <span v-if="loading" class="flex items-center gap-2">
              <svg class="animate-spin w-4 h-4" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"/>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"/>
              </svg>
              Entrando...
            </span>
            <span v-else>Entrar</span>
          </button>
        </form>

        <div class="divider my-6"></div>

        <p class="text-center text-sm text-slate-400">
          Não tem uma conta?
          <router-link to="/register" class="text-emerald-400 hover:text-emerald-300 font-medium transition-colors ml-1">
            Criar conta
          </router-link>
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

const router = useRouter()
const email = ref('')
const password = ref('')
const rememberMe = ref(false)
const showPassword = ref(false)
const loading = ref(false)
const error = ref('')

const handleLogin = async () => {
  error.value = ''
  loading.value = true
  try {
    const response = await axios.post('http://localhost:5000/api/users/login', {
      email: email.value,
      password: password.value
    })
    localStorage.setItem('token', response.data.token)
    router.push('/profile')
  } catch {
    error.value = 'E-mail ou senha incorretos. Tente novamente.'
  } finally {
    loading.value = false
  }
}
</script>
