<template>
  <nav
    class="fixed top-0 left-0 right-0 z-50 transition-all duration-300"
    :class="scrolled
      ? 'bg-surface-900/95 backdrop-blur-xl border-b border-slate-700/50 shadow-xl'
      : 'bg-transparent'"
  >
    <div class="max-w-7xl mx-auto px-6">
      <div class="flex items-center justify-between h-16">
        <!-- Logo -->
        <router-link to="/" class="flex items-center gap-2.5 group">
          <div class="w-8 h-8 bg-emerald-500 rounded-lg flex items-center justify-center text-base transition-transform group-hover:scale-110">
            🍃
          </div>
          <span class="text-lg font-bold text-white">FruitCo</span>
        </router-link>

        <!-- Nav Links (desktop) -->
        <div class="hidden md:flex items-center gap-8">
          <router-link to="/" class="nav-link" :class="{ 'nav-link-active': $route.path === '/' }">
            Produtos
          </router-link>
          <router-link to="/cart" class="nav-link" :class="{ 'nav-link-active': $route.path === '/cart' }">
            Carrinho
          </router-link>
        </div>

        <!-- Right actions -->
        <div class="flex items-center gap-3">
          <!-- Cart -->
          <router-link
            to="/cart"
            class="relative flex items-center justify-center w-10 h-10 rounded-xl text-slate-400 hover:text-white hover:bg-surface-700 transition-all duration-200"
          >
            <i class="fas fa-shopping-cart text-base"></i>
            <span
              v-if="cartItemCount > 0"
              class="absolute -top-1 -right-1 bg-emerald-500 text-white text-[10px] font-bold rounded-full min-w-[18px] h-[18px] flex items-center justify-center px-1 shadow-glow-sm"
            >
              {{ cartItemCount > 99 ? '99+' : cartItemCount }}
            </span>
          </router-link>

          <!-- Authenticated user -->
          <template v-if="isAuthenticated">
            <div class="relative" ref="dropdownRef">
              <button
                @click="toggleDropdown"
                class="flex items-center gap-2 px-3 py-2 rounded-xl text-slate-300 hover:text-white hover:bg-surface-700 transition-all duration-200 text-sm font-medium"
              >
                <div class="w-6 h-6 bg-emerald-500/20 border border-emerald-500/30 rounded-full flex items-center justify-center">
                  <i class="fas fa-user text-emerald-400 text-xs"></i>
                </div>
                <span class="hidden md:inline">{{ user?.firstName || 'Conta' }}</span>
                <i class="fas fa-chevron-down text-xs transition-transform duration-200" :class="{ 'rotate-180': isDropdownOpen }"></i>
              </button>

              <!-- Dropdown -->
              <transition name="dropdown">
                <div
                  v-if="isDropdownOpen"
                  class="absolute right-0 top-full mt-2 w-48 bg-surface-800 border border-slate-700 rounded-xl shadow-2xl overflow-hidden"
                >
                  <router-link
                    to="/profile"
                    @click="isDropdownOpen = false"
                    class="flex items-center gap-3 px-4 py-3 text-sm text-slate-300 hover:text-white hover:bg-surface-700 transition-colors"
                  >
                    <i class="fas fa-user-circle w-4 text-slate-400"></i>
                    Meu Perfil
                  </router-link>
                  <div class="divider mx-2"></div>
                  <button
                    @click="handleLogout"
                    class="w-full flex items-center gap-3 px-4 py-3 text-sm text-red-400 hover:text-red-300 hover:bg-red-500/10 transition-colors"
                  >
                    <i class="fas fa-sign-out-alt w-4"></i>
                    Sair
                  </button>
                </div>
              </transition>
            </div>
          </template>

          <!-- Guest -->
          <template v-else>
            <router-link to="/login" class="btn btn-primary text-xs px-4 py-2">
              <i class="fas fa-sign-in-alt"></i>
              Entrar
            </router-link>
          </template>
        </div>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'
import { useCartStore } from '@/stores/cart'
import axios from 'axios'

const router = useRouter()
const cartStore = useCartStore()
const scrolled = ref(false)
const isDropdownOpen = ref(false)
const dropdownRef = ref(null)
const user = ref(null)

const cartItemCount = computed(() => cartStore.totalItems)
const isAuthenticated = computed(() => !!localStorage.getItem('token'))

const toggleDropdown = () => {
  isDropdownOpen.value = !isDropdownOpen.value
}

const handleLogout = () => {
  localStorage.removeItem('token')
  isDropdownOpen.value = false
  router.push('/login')
}

const fetchUserProfile = async () => {
  try {
    const token = localStorage.getItem('token')
    if (!token) return
    const response = await axios.get('http://localhost:5000/api/users/profile', {
      headers: { Authorization: `Bearer ${token}` }
    })
    user.value = response.data
  } catch {}
}

const handleScroll = () => {
  scrolled.value = window.scrollY > 20
}

const handleClickOutside = (e) => {
  if (dropdownRef.value && !dropdownRef.value.contains(e.target)) {
    isDropdownOpen.value = false
  }
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
  document.addEventListener('click', handleClickOutside)
  if (isAuthenticated.value) fetchUserProfile()
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
  document.removeEventListener('click', handleClickOutside)
})
</script>

<style scoped>
.dropdown-enter-active,
.dropdown-leave-active {
  transition: opacity 0.15s ease, transform 0.15s ease;
}
.dropdown-enter-from,
.dropdown-leave-to {
  opacity: 0;
  transform: translateY(-6px) scale(0.97);
}
</style>
