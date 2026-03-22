<template>
  <div class="min-h-screen px-6 py-12">
    <div class="max-w-6xl mx-auto">
      <!-- Header -->
      <div class="mb-8 animate-fade-in">
        <h1 class="text-3xl font-bold text-white">Seu Carrinho</h1>
        <p class="text-slate-400 text-sm mt-1">
          {{ cartStore.totalItems }} {{ cartStore.totalItems === 1 ? 'item' : 'itens' }}
        </p>
      </div>

      <!-- Empty state -->
      <div v-if="cartStore.items.length === 0" class="text-center py-24 animate-scale-in">
        <div class="w-20 h-20 bg-surface-800 border border-slate-700 rounded-2xl flex items-center justify-center mx-auto mb-6">
          <i class="fas fa-shopping-cart text-3xl text-slate-500"></i>
        </div>
        <h2 class="text-xl font-semibold text-white mb-2">Carrinho vazio</h2>
        <p class="text-slate-400 text-sm mb-8">Você ainda não adicionou nenhum produto.</p>
        <router-link to="/" class="btn btn-primary">
          <i class="fas fa-arrow-left"></i>
          Continuar Comprando
        </router-link>
      </div>

      <!-- Cart content -->
      <div v-else class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <!-- Items list -->
        <div class="lg:col-span-2 space-y-3">
          <transition-group name="cart-item" tag="div" class="space-y-3">
            <div
              v-for="item in cartStore.items"
              :key="item.id"
              class="card p-4 flex items-center gap-4 animate-slide-up"
            >
              <!-- Image -->
              <div class="w-18 h-18 rounded-xl overflow-hidden bg-surface-700 flex-shrink-0">
                <img
                  :src="item.image"
                  :alt="item.name"
                  @error="(e) => e.target.src = 'https://images.unsplash.com/photo-1610832958506-aa56368176cf?w=200&q=80'"
                  class="w-full h-full object-cover"
                  style="width:72px;height:72px"
                />
              </div>

              <!-- Info -->
              <div class="flex-1 min-w-0">
                <h3 class="font-semibold text-white text-sm truncate">{{ item.name }}</h3>
                <p class="text-slate-400 text-xs mt-0.5 capitalize">{{ item.category }}</p>
                <p class="text-emerald-400 font-bold text-sm mt-1">R${{ formatPrice(item.price) }}</p>
              </div>

              <!-- Quantity controls -->
              <div class="flex items-center gap-1 bg-surface-700 border border-slate-600 rounded-xl p-1">
                <button
                  @click="updateQuantity(item.id, item.quantity - 1)"
                  :disabled="item.quantity <= 1"
                  class="w-7 h-7 rounded-lg flex items-center justify-center text-slate-400 hover:text-white hover:bg-surface-600 disabled:opacity-30 disabled:cursor-not-allowed transition-all"
                >
                  <i class="fas fa-minus text-xs"></i>
                </button>
                <span class="w-8 text-center text-white text-sm font-semibold">{{ item.quantity }}</span>
                <button
                  @click="updateQuantity(item.id, item.quantity + 1)"
                  class="w-7 h-7 rounded-lg flex items-center justify-center text-slate-400 hover:text-white hover:bg-surface-600 transition-all"
                >
                  <i class="fas fa-plus text-xs"></i>
                </button>
              </div>

              <!-- Subtotal -->
              <div class="text-right flex-shrink-0 hidden sm:block">
                <p class="text-white font-bold text-sm">R${{ formatPrice(item.price * item.quantity) }}</p>
                <p class="text-slate-500 text-xs">subtotal</p>
              </div>

              <!-- Remove -->
              <button
                @click="removeItem(item.id)"
                class="w-8 h-8 rounded-xl flex items-center justify-center text-slate-500 hover:text-red-400 hover:bg-red-500/10 transition-all flex-shrink-0"
              >
                <i class="fas fa-trash-alt text-xs"></i>
              </button>
            </div>
          </transition-group>

          <!-- Continue shopping -->
          <div class="pt-2">
            <router-link to="/" class="btn btn-ghost text-sm">
              <i class="fas fa-arrow-left text-xs"></i>
              Continuar Comprando
            </router-link>
          </div>
        </div>

        <!-- Order Summary -->
        <div class="lg:col-span-1">
          <div class="card p-6 sticky top-20 animate-slide-up delay-200">
            <h2 class="text-lg font-bold text-white mb-5">Resumo do Pedido</h2>

            <div class="space-y-3 mb-5">
              <div class="flex justify-between text-sm">
                <span class="text-slate-400">Subtotal ({{ cartStore.totalItems }} itens)</span>
                <span class="text-white font-medium">R${{ formatPrice(cartStore.totalPrice) }}</span>
              </div>
              <div class="flex justify-between text-sm">
                <span class="text-slate-400">Frete</span>
                <span class="text-emerald-400 font-medium">Grátis</span>
              </div>
            </div>

            <div class="divider mb-5"></div>

            <div class="flex justify-between mb-6">
              <span class="text-white font-semibold">Total</span>
              <div class="text-right">
                <div class="text-2xl font-black text-emerald-400">R${{ formatPrice(cartStore.totalPrice) }}</div>
                <div class="text-slate-500 text-xs">à vista</div>
              </div>
            </div>

            <button @click="checkout" class="btn btn-primary w-full text-sm py-3">
              <i class="fas fa-lock text-xs"></i>
              Finalizar Pedido
            </button>

            <!-- Security -->
            <div class="flex items-center justify-center gap-4 mt-4">
              <div class="flex items-center gap-1.5 text-slate-500 text-xs">
                <i class="fas fa-shield-alt text-emerald-500/60"></i>
                Pagamento seguro
              </div>
              <div class="flex items-center gap-1.5 text-slate-500 text-xs">
                <i class="fas fa-lock text-emerald-500/60"></i>
                SSL
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { useCartStore } from '@/stores/cart'
import { useRouter } from 'vue-router'

const cartStore = useCartStore()
const router = useRouter()

const formatPrice = (val) => parseFloat(val).toFixed(2).replace('.', ',')

const updateQuantity = (id, qty) => {
  if (qty > 0) cartStore.updateQuantity(id, qty)
}

const removeItem = (id) => {
  cartStore.removeFromCart(id)
}

const checkout = () => {
  router.push('/checkout')
}
</script>

<style scoped>
.cart-item-enter-active,
.cart-item-leave-active {
  transition: all 0.3s ease;
}
.cart-item-enter-from,
.cart-item-leave-to {
  opacity: 0;
  transform: translateX(-16px);
}
</style>
