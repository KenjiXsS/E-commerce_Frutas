<template>
  <div class="min-h-screen px-6 py-12">
    <div class="max-w-5xl mx-auto">
      <!-- Header -->
      <div class="mb-8 animate-fade-in">
        <h1 class="text-3xl font-bold text-white">Meu Perfil</h1>
        <p class="text-slate-400 text-sm mt-1">Gerencie sua conta e visualize seus itens salvos</p>
      </div>

      <!-- Loading -->
      <div v-if="loading" class="flex items-center justify-center py-24">
        <div class="flex flex-col items-center gap-3">
          <svg class="animate-spin w-8 h-8 text-emerald-500" fill="none" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"/>
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"/>
          </svg>
          <p class="text-slate-400 text-sm">Carregando...</p>
        </div>
      </div>

      <div v-else class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <!-- Left: User info -->
        <div class="lg:col-span-2 space-y-5">
          <!-- Avatar & Name card -->
          <div class="card p-6 animate-slide-up">
            <div class="flex items-center gap-5">
              <div class="w-16 h-16 bg-emerald-500/10 border border-emerald-500/20 rounded-2xl flex items-center justify-center text-2xl font-bold text-emerald-400 flex-shrink-0">
                {{ userInitials }}
              </div>
              <div>
                <h2 class="text-xl font-bold text-white">{{ fullName }}</h2>
                <p class="text-slate-400 text-sm">{{ user?.email || '—' }}</p>
                <span class="badge badge-emerald mt-2 text-[11px]">
                  <i class="fas fa-check mr-1"></i>Conta verificada
                </span>
              </div>
            </div>
          </div>

          <!-- Personal Info -->
          <div class="card p-6 animate-slide-up delay-100">
            <div class="flex items-center justify-between mb-5">
              <h3 class="font-semibold text-white">Informações Pessoais</h3>
            </div>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
              <div>
                <p class="label mb-1">Primeiro Nome</p>
                <p class="text-white text-sm font-medium">{{ user?.firstName || '—' }}</p>
              </div>
              <div>
                <p class="label mb-1">Sobrenome</p>
                <p class="text-white text-sm font-medium">{{ user?.lastName || '—' }}</p>
              </div>
              <div class="sm:col-span-2">
                <p class="label mb-1">E-mail</p>
                <p class="text-white text-sm font-medium">{{ user?.email || '—' }}</p>
              </div>
            </div>
          </div>

          <!-- Address -->
          <div class="card p-6 animate-slide-up delay-200">
            <h3 class="font-semibold text-white mb-5">Endereço</h3>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
              <div class="sm:col-span-2">
                <p class="label mb-1">Rua</p>
                <p class="text-white text-sm font-medium">{{ user?.address?.street || '—' }}</p>
              </div>
              <div>
                <p class="label mb-1">Cidade</p>
                <p class="text-white text-sm font-medium">{{ user?.address?.city || '—' }}</p>
              </div>
              <div>
                <p class="label mb-1">Estado</p>
                <p class="text-white text-sm font-medium">{{ user?.address?.state || '—' }}</p>
              </div>
              <div>
                <p class="label mb-1">CEP</p>
                <p class="text-white text-sm font-medium">{{ user?.address?.postalCode || '—' }}</p>
              </div>
              <div>
                <p class="label mb-1">País</p>
                <p class="text-white text-sm font-medium">{{ user?.address?.country || '—' }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- Right: Saved items & actions -->
        <div class="space-y-5">
          <!-- Saved Items -->
          <div class="card p-5 animate-slide-up delay-100">
            <h3 class="font-semibold text-white mb-4">Itens Salvos</h3>

            <div v-if="savedItems.length > 0" class="space-y-3">
              <div
                v-for="item in savedItems"
                :key="item.id"
                class="flex items-center gap-3 p-3 bg-surface-700 rounded-xl"
              >
                <img
                  :src="item.image"
                  :alt="item.name"
                  class="w-12 h-12 object-cover rounded-lg flex-shrink-0"
                  @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1610832958506-aa56368176cf?w=100&q=80'"
                />
                <div class="flex-1 min-w-0">
                  <p class="text-white text-sm font-medium truncate">{{ item.name }}</p>
                  <p class="text-emerald-400 text-xs font-semibold">R${{ item.price }}</p>
                </div>
                <button
                  @click="removeSavedItem(item.id)"
                  class="w-7 h-7 rounded-lg flex items-center justify-center text-slate-500 hover:text-red-400 hover:bg-red-500/10 transition-all"
                >
                  <i class="fas fa-times text-xs"></i>
                </button>
              </div>
            </div>

            <div v-else class="text-center py-8">
              <div class="text-2xl mb-3">🍓</div>
              <p class="text-slate-400 text-sm">Nenhum item salvo</p>
              <router-link to="/" class="btn btn-primary text-xs mt-4">
                Ver Produtos
              </router-link>
            </div>
          </div>

          <!-- Account Actions -->
          <div class="card p-5 animate-slide-up delay-200">
            <h3 class="font-semibold text-white mb-4">Conta</h3>
            <div class="space-y-2">
              <button class="w-full flex items-center gap-3 px-4 py-3 bg-surface-700 hover:bg-surface-600 rounded-xl text-slate-300 hover:text-white transition-all text-sm">
                <i class="fas fa-edit w-4 text-slate-400"></i>
                Editar Perfil
              </button>
              <button
                @click="logout"
                class="w-full flex items-center gap-3 px-4 py-3 bg-red-500/10 hover:bg-red-500/20 rounded-xl text-red-400 hover:text-red-300 transition-all border border-red-500/10 text-sm"
              >
                <i class="fas fa-sign-out-alt w-4"></i>
                Sair da Conta
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

const router = useRouter()
const user = ref(null)
const savedItems = ref([])
const loading = ref(true)

const fullName = computed(() => {
  if (!user.value) return ''
  return [user.value.firstName, user.value.lastName].filter(Boolean).join(' ') || 'Usuário'
})

const userInitials = computed(() => {
  if (!user.value) return '?'
  const f = user.value.firstName?.[0] || ''
  const l = user.value.lastName?.[0] || ''
  return (f + l).toUpperCase() || '?'
})

const fetchUserProfile = async () => {
  const token = localStorage.getItem('token')
  if (!token) { router.push('/login'); return }
  try {
    const res = await axios.get('http://localhost:5000/api/users/profile', {
      headers: { Authorization: `Bearer ${token}` }
    })
    user.value = res.data
  } catch {
    router.push('/login')
  }
}

const fetchSavedItems = async () => {
  const token = localStorage.getItem('token')
  if (!token) return
  try {
    const res = await axios.get('http://localhost:5000/api/users/saved-items', {
      headers: { Authorization: `Bearer ${token}` }
    })
    savedItems.value = res.data
  } catch {}
}

const removeSavedItem = async (itemId) => {
  const token = localStorage.getItem('token')
  if (!token) return
  try {
    await axios.delete(`http://localhost:5000/api/users/saved-items/${itemId}`, {
      headers: { Authorization: `Bearer ${token}` }
    })
    savedItems.value = savedItems.value.filter(i => i.id !== itemId)
  } catch {}
}

const logout = () => {
  localStorage.removeItem('token')
  router.push('/login')
}

onMounted(async () => {
  await fetchUserProfile()
  await fetchSavedItems()
  loading.value = false
})
</script>
