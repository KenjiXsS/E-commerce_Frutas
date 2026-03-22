<template>
  <div>
    <!-- Hero Section -->
    <section class="relative overflow-hidden pt-24 pb-20 px-6">
      <!-- Background glow -->
      <div class="absolute inset-0 pointer-events-none">
        <div class="absolute top-0 left-1/2 -translate-x-1/2 w-[800px] h-[400px] bg-emerald-500/8 rounded-full blur-3xl" />
      </div>

      <div class="max-w-7xl mx-auto relative">
        <div class="text-center max-w-3xl mx-auto">
          <div class="inline-flex items-center gap-2 px-3 py-1.5 bg-emerald-500/10 border border-emerald-500/20 rounded-full text-emerald-400 text-xs font-medium mb-6 animate-fade-in">
            <span class="w-1.5 h-1.5 bg-emerald-400 rounded-full animate-pulse"></span>
            Colhidas hoje, entregues amanhã
          </div>

          <h1 class="text-5xl md:text-6xl font-black text-white leading-tight mb-6 animate-slide-up">
            Frutas Frescas
            <span class="text-gradient block">Premium</span>
          </h1>

          <p class="text-slate-400 text-lg leading-relaxed mb-8 animate-slide-up delay-100">
            Descubra nossa seleção de frutas selecionadas com cuidado, direto dos melhores produtores do Brasil.
          </p>

          <!-- Stats -->
          <div class="flex items-center justify-center gap-8 text-center animate-fade-in delay-200">
            <div v-for="stat in stats" :key="stat.label">
              <div class="text-2xl font-bold text-white">{{ stat.value }}</div>
              <div class="text-slate-500 text-xs mt-0.5">{{ stat.label }}</div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Products Section -->
    <section class="px-6 pb-20">
      <div class="max-w-7xl mx-auto">
        <!-- Filter bar -->
        <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 mb-8">
          <div>
            <h2 class="text-xl font-bold text-white">Nossos Produtos</h2>
            <p class="text-slate-500 text-sm mt-0.5">{{ filteredProducts.length }} produtos encontrados</p>
          </div>

          <!-- Categories -->
          <div class="flex items-center gap-2 flex-wrap">
            <button
              v-for="cat in categories"
              :key="cat.value"
              @click="filterByCategory(cat.value)"
              class="text-xs font-medium px-3.5 py-2 rounded-xl border transition-all duration-200"
              :class="selectedCategory === cat.value
                ? 'bg-emerald-500 border-emerald-500 text-white shadow-glow-sm'
                : 'bg-surface-800 border-slate-700 text-slate-400 hover:border-slate-500 hover:text-slate-200'"
            >
              {{ cat.label }}
            </button>
          </div>
        </div>

        <!-- Product Grid -->
        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-5">
          <ProductCard
            v-for="(product, index) in paginatedProducts"
            :key="product.id"
            :product="product"
            class="animate-slide-up"
            :style="{ animationDelay: `${index * 50}ms` }"
          />
        </div>

        <!-- Empty state -->
        <div v-if="paginatedProducts.length === 0" class="text-center py-20">
          <div class="text-4xl mb-4">🥭</div>
          <p class="text-slate-400 text-lg">Nenhum produto encontrado nessa categoria.</p>
        </div>

        <!-- Pagination -->
        <div v-if="totalPages > 1" class="mt-12 flex justify-center items-center gap-2">
          <button
            @click="previousPage"
            :disabled="currentPage === 1"
            class="w-9 h-9 rounded-xl border border-slate-700 text-slate-400 hover:text-white hover:border-slate-500 disabled:opacity-30 disabled:cursor-not-allowed transition-all flex items-center justify-center"
          >
            <i class="fas fa-chevron-left text-xs"></i>
          </button>

          <button
            v-for="page in totalPages"
            :key="page"
            @click="goToPage(page)"
            class="w-9 h-9 rounded-xl text-sm font-medium transition-all duration-200"
            :class="currentPage === page
              ? 'bg-emerald-500 text-white shadow-glow-sm'
              : 'border border-slate-700 text-slate-400 hover:text-white hover:border-slate-500'"
          >
            {{ page }}
          </button>

          <button
            @click="nextPage"
            :disabled="currentPage === totalPages"
            class="w-9 h-9 rounded-xl border border-slate-700 text-slate-400 hover:text-white hover:border-slate-500 disabled:opacity-30 disabled:cursor-not-allowed transition-all flex items-center justify-center"
          >
            <i class="fas fa-chevron-right text-xs"></i>
          </button>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import ProductCard from '@/components/ProductCard.vue'
import { products } from '@/data/products.js'

const categories = [
  { value: 'All', label: 'Todos' },
  { value: 'frutas', label: 'Frutas' },
  { value: 'frutas tropicais', label: 'Tropicais' },
  { value: 'frutas cítricas', label: 'Cítricas' },
  { value: 'frutas vermelhas', label: 'Vermelhas' },
  { value: 'frutas exóticas', label: 'Exóticas' },
]

const stats = [
  { value: '25+', label: 'Variedades' },
  { value: '1.2k+', label: 'Clientes' },
  { value: '4.9★', label: 'Avaliação' },
]

const selectedCategory = ref('All')
const currentPage = ref(1)
const itemsPerPage = 8

const filteredProducts = computed(() => {
  if (selectedCategory.value === 'All') return products
  return products.filter(p => p.category === selectedCategory.value)
})

const totalPages = computed(() => Math.ceil(filteredProducts.value.length / itemsPerPage))

const paginatedProducts = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage
  return filteredProducts.value.slice(start, start + itemsPerPage)
})

const filterByCategory = (cat) => {
  selectedCategory.value = cat
  currentPage.value = 1
}

const goToPage = (page) => { currentPage.value = page }
const nextPage = () => { if (currentPage.value < totalPages.value) currentPage.value++ }
const previousPage = () => { if (currentPage.value > 1) currentPage.value-- }
</script>
