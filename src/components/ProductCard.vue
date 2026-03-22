<template>
  <div
    class="card group flex flex-col overflow-hidden hover:border-emerald-500/30 hover:-translate-y-1 hover:shadow-card-hover cursor-pointer"
    @click="addToCart"
  >
    <!-- Image -->
    <div class="relative overflow-hidden bg-surface-700 h-52">
      <img
        :src="product.image"
        :alt="product.name"
        @error="handleImageError"
        class="w-full h-full object-cover transition-transform duration-500 group-hover:scale-105"
      />
      <!-- Overlay -->
      <div class="absolute inset-0 bg-gradient-to-t from-surface-800/80 via-transparent to-transparent opacity-0 group-hover:opacity-100 transition-opacity duration-300" />

      <!-- Category badge -->
      <div class="absolute top-3 left-3">
        <span class="badge badge-emerald capitalize text-[11px]">
          {{ product.category }}
        </span>
      </div>

      <!-- Rating -->
      <div class="absolute top-3 right-3 flex items-center gap-1 px-2 py-1 bg-surface-900/80 backdrop-blur-sm rounded-lg border border-slate-700/50">
        <i class="fas fa-star text-yellow-400 text-xs"></i>
        <span class="text-white text-xs font-semibold">{{ product.rating }}</span>
      </div>

      <!-- Quick add (appears on hover) -->
      <div class="absolute inset-x-0 bottom-0 flex items-center justify-center pb-4 opacity-0 group-hover:opacity-100 translate-y-2 group-hover:translate-y-0 transition-all duration-300">
        <button
          @click.stop="addToCart"
          class="btn btn-primary text-xs px-5 py-2 shadow-glow"
        >
          <i class="fas fa-plus text-xs"></i>
          Adicionar
        </button>
      </div>
    </div>

    <!-- Content -->
    <div class="p-4 flex flex-col flex-1">
      <h3 class="font-semibold text-white text-sm mb-1 leading-tight">{{ product.name }}</h3>
      <p class="text-slate-400 text-xs leading-relaxed line-clamp-2 flex-1 mb-3">{{ product.description }}</p>

      <div class="flex items-center justify-between mt-auto">
        <div>
          <span class="text-emerald-400 font-bold text-lg">R${{ formatPrice(product.price) }}</span>
          <span class="text-slate-500 text-xs ml-1">/ kg</span>
        </div>
        <!-- Stock indicator -->
        <span v-if="product.stock <= 10" class="text-xs text-orange-400 font-medium">
          Últimas {{ product.stock }} un.
        </span>
        <span v-else class="text-xs text-emerald-500">
          <i class="fas fa-check mr-1"></i>Em estoque
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { useCartStore } from '@/stores/cart'

const props = defineProps({
  product: {
    type: Object,
    required: true
  }
})

const cartStore = useCartStore()

const handleImageError = (e) => {
  e.target.src = 'https://images.unsplash.com/photo-1610832958506-aa56368176cf?w=400&q=80'
}

const formatPrice = (price) => {
  return parseFloat(price).toFixed(2).replace('.', ',')
}

const addToCart = () => {
  cartStore.addToCart(props.product)
}
</script>
