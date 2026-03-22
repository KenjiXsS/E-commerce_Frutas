<template>
  <div class="min-h-screen bg-surface-950 flex flex-col">
    <Navbar />

    <main class="flex-1 pt-16">
      <router-view v-slot="{ Component }">
        <transition name="page" mode="out-in">
          <component :is="Component" />
        </transition>
      </router-view>
    </main>

    <!-- Footer -->
    <footer class="mt-24 border-t border-slate-800">
      <div class="max-w-7xl mx-auto px-6 py-16">
        <!-- Top section -->
        <div class="grid grid-cols-1 md:grid-cols-4 gap-12 mb-12">
          <!-- Brand -->
          <div class="md:col-span-1">
            <div class="flex items-center gap-2.5 mb-4">
              <div class="w-8 h-8 bg-emerald-500 rounded-lg flex items-center justify-center text-base">🍃</div>
              <span class="text-lg font-bold text-white">FruitCo</span>
            </div>
            <p class="text-slate-400 text-sm leading-relaxed">
              Frutas frescas e premium entregues direto na sua porta. Qualidade garantida.
            </p>
          </div>

          <!-- Features -->
          <div v-for="feature in features" :key="feature.title" class="flex items-start gap-4">
            <div class="w-10 h-10 bg-emerald-500/10 border border-emerald-500/20 rounded-xl flex items-center justify-center flex-shrink-0">
              <i :class="feature.icon" class="text-emerald-400 text-sm"></i>
            </div>
            <div>
              <h3 class="font-semibold text-white text-sm mb-1">{{ feature.title }}</h3>
              <p class="text-slate-400 text-sm">{{ feature.description }}</p>
            </div>
          </div>
        </div>

        <!-- Bottom -->
        <div class="divider pt-8 flex flex-col sm:flex-row items-center justify-between gap-4">
          <p class="text-slate-500 text-sm">© 2026 FruitCo. Todos os direitos reservados.</p>
          <div class="flex items-center gap-6">
            <span class="text-slate-500 text-xs">Pagamento seguro</span>
            <div class="flex items-center gap-2">
              <span class="text-slate-400 text-xs px-2 py-1 bg-surface-800 rounded border border-slate-700">VISA</span>
              <span class="text-slate-400 text-xs px-2 py-1 bg-surface-800 rounded border border-slate-700">MASTER</span>
              <span class="text-slate-400 text-xs px-2 py-1 bg-surface-800 rounded border border-slate-700">PIX</span>
            </div>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup>
import Navbar from '@/components/Navbar.vue'

const features = [
  {
    icon: 'fas fa-award',
    title: 'Alta Qualidade',
    description: 'Selecionadas com cuidado para garantir frescor'
  },
  {
    icon: 'fas fa-truck',
    title: 'Frete Grátis',
    description: 'Em pedidos acima de R$150'
  },
  {
    icon: 'fas fa-headset',
    title: 'Suporte 24/7',
    description: 'Estamos sempre aqui para você'
  }
]
</script>

<style>
.page-enter-active,
.page-leave-active {
  transition: opacity 0.25s ease, transform 0.25s ease;
}

.page-enter-from {
  opacity: 0;
  transform: translateY(8px);
}

.page-leave-to {
  opacity: 0;
  transform: translateY(-8px);
}
</style>
