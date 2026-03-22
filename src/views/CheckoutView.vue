<template>
  <div class="min-h-screen px-6 py-12">
    <div class="max-w-6xl mx-auto">
      <!-- Header -->
      <div class="mb-8 animate-fade-in">
        <div class="flex items-center gap-3 mb-1">
          <router-link to="/cart" class="text-slate-400 hover:text-white transition-colors text-sm">
            <i class="fas fa-arrow-left mr-1"></i>Carrinho
          </router-link>
          <i class="fas fa-chevron-right text-slate-600 text-xs"></i>
          <span class="text-white text-sm font-medium">Checkout</span>
        </div>
        <h1 class="text-3xl font-bold text-white mt-3">Finalizar Pedido</h1>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-5 gap-6">
        <!-- Left: Payment form -->
        <div class="lg:col-span-3 space-y-5">
          <!-- Billing details -->
          <div class="card p-6 animate-slide-up">
            <h2 class="font-semibold text-white mb-5 flex items-center gap-2">
              <i class="fas fa-user text-emerald-400 text-sm"></i>
              Dados de Cobrança
            </h2>

            <form id="payment-form" @submit.prevent="handleSubmit">
              <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-4">
                <div>
                  <label class="label">Primeiro Nome</label>
                  <input v-model="billingDetails.firstName" type="text" class="input" placeholder="João" required />
                </div>
                <div>
                  <label class="label">Sobrenome</label>
                  <input v-model="billingDetails.lastName" type="text" class="input" placeholder="Silva" required />
                </div>
                <div class="sm:col-span-2">
                  <label class="label">E-mail</label>
                  <input v-model="billingDetails.email" type="email" class="input" placeholder="joao@email.com" required />
                </div>
                <div class="sm:col-span-2">
                  <label class="label">Endereço</label>
                  <input v-model="billingDetails.address" type="text" class="input" placeholder="Rua das Flores, 123" required />
                </div>
                <div>
                  <label class="label">Cidade</label>
                  <input v-model="billingDetails.city" type="text" class="input" placeholder="São Paulo" required />
                </div>
                <div>
                  <label class="label">CEP</label>
                  <input v-model="billingDetails.postalCode" type="text" class="input" placeholder="00000-000" required />
                </div>
              </div>
            </form>
          </div>

          <!-- Card payment -->
          <div class="card p-6 animate-slide-up delay-100">
            <h2 class="font-semibold text-white mb-5 flex items-center gap-2">
              <i class="fas fa-credit-card text-emerald-400 text-sm"></i>
              Dados do Cartão
            </h2>

            <div class="space-y-4">
              <div>
                <label class="label">Número do Cartão</label>
                <div id="card-element" class="input py-3 bg-surface-700"></div>
                <div id="card-errors" class="text-red-400 text-xs mt-1.5" role="alert"></div>
              </div>
            </div>

            <!-- Security badges -->
            <div class="flex items-center gap-5 mt-5 pt-5 border-t border-slate-700/50">
              <div class="flex items-center gap-2 text-slate-500 text-xs">
                <i class="fas fa-shield-alt text-emerald-500/60"></i>
                Pagamento seguro
              </div>
              <div class="flex items-center gap-2 text-slate-500 text-xs">
                <i class="fas fa-lock text-emerald-500/60"></i>
                Criptografia SSL
              </div>
              <div class="flex items-center gap-2 text-slate-500 text-xs">
                <i class="fas fa-check-circle text-emerald-500/60"></i>
                PCI compliant
              </div>
            </div>
          </div>

          <!-- Submit -->
          <button
            form="payment-form"
            type="submit"
            @click.prevent="handleSubmit"
            class="btn btn-primary w-full py-4 text-base"
            :disabled="processing"
          >
            <span v-if="processing" class="flex items-center gap-2">
              <svg class="animate-spin w-4 h-4" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"/>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"/>
              </svg>
              Processando...
            </span>
            <span v-else class="flex items-center gap-2">
              <i class="fas fa-lock text-sm"></i>
              Pagar R${{ formatPrice(cartStore.totalPrice) }}
            </span>
          </button>
        </div>

        <!-- Right: Order summary -->
        <div class="lg:col-span-2">
          <div class="card p-6 sticky top-20 animate-slide-up delay-200">
            <h2 class="font-semibold text-white mb-5">Resumo</h2>

            <div class="space-y-3 mb-5 max-h-64 overflow-y-auto pr-1">
              <div
                v-for="item in cartStore.items"
                :key="item.id"
                class="flex items-center gap-3"
              >
                <div class="relative flex-shrink-0">
                  <img
                    :src="item.image"
                    :alt="item.name"
                    class="w-12 h-12 object-cover rounded-xl bg-surface-700"
                    @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1610832958506-aa56368176cf?w=100&q=80'"
                  />
                  <span class="absolute -top-1.5 -right-1.5 w-5 h-5 bg-emerald-500 text-white text-[10px] font-bold rounded-full flex items-center justify-center">
                    {{ item.quantity }}
                  </span>
                </div>
                <div class="flex-1 min-w-0">
                  <p class="text-white text-sm font-medium truncate">{{ item.name }}</p>
                </div>
                <span class="text-slate-300 text-sm font-semibold flex-shrink-0">
                  R${{ formatPrice(item.price * item.quantity) }}
                </span>
              </div>
            </div>

            <div class="divider mb-4"></div>

            <div class="space-y-2.5 mb-5">
              <div class="flex justify-between text-sm">
                <span class="text-slate-400">Subtotal</span>
                <span class="text-white">R${{ formatPrice(cartStore.totalPrice) }}</span>
              </div>
              <div class="flex justify-between text-sm">
                <span class="text-slate-400">Frete</span>
                <span class="text-emerald-400">Grátis</span>
              </div>
            </div>

            <div class="divider mb-4"></div>

            <div class="flex justify-between">
              <span class="text-white font-semibold">Total</span>
              <span class="text-emerald-400 font-bold text-xl">R${{ formatPrice(cartStore.totalPrice) }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useCartStore } from '@/stores/cart'
import { loadStripe } from '@stripe/stripe-js'
import { useRouter } from 'vue-router'

const cartStore = useCartStore()
const router = useRouter()
const stripe = ref(null)
const elements = ref(null)
const card = ref(null)
const processing = ref(false)

const billingDetails = ref({
  firstName: '',
  lastName: '',
  email: '',
  address: '',
  city: '',
  postalCode: ''
})

const formatPrice = (val) => parseFloat(val).toFixed(2).replace('.', ',')

onMounted(async () => {
  stripe.value = await loadStripe('YOUR_PUBLISHABLE_KEY')
  elements.value = stripe.value.elements()
  card.value = elements.value.create('card', {
    style: {
      base: {
        fontSize: '14px',
        color: '#f1f5f9',
        fontFamily: 'Inter, system-ui, sans-serif',
        '::placeholder': { color: '#64748b' },
        backgroundColor: 'transparent',
      },
      invalid: { color: '#f87171' }
    }
  })
  card.value.mount('#card-element')
  card.value.on('change', (event) => {
    const el = document.getElementById('card-errors')
    el.textContent = event.error?.message || ''
  })
})

const handleSubmit = async () => {
  if (!stripe.value || !elements.value) return
  processing.value = true
  try {
    const { error, paymentMethod } = await stripe.value.createPaymentMethod({
      type: 'card',
      card: card.value,
      billing_details: billingDetails.value
    })
    if (error) {
      document.getElementById('card-errors').textContent = error.message
      processing.value = false
    } else {
      console.log('PaymentMethod:', paymentMethod)
      setTimeout(() => {
        cartStore.clearCart()
        router.push('/success')
      }, 2000)
    }
  } catch {
    processing.value = false
  }
}
</script>
