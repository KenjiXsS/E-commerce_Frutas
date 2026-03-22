<template>
  <div class="min-h-screen flex items-center justify-center px-6 py-16 relative">
    <!-- Background -->
    <div class="absolute inset-0 pointer-events-none overflow-hidden">
      <div class="absolute top-1/3 left-1/2 -translate-x-1/2 w-[500px] h-[400px] bg-emerald-500/8 rounded-full blur-3xl" />
    </div>

    <div class="w-full max-w-md text-center relative animate-scale-in">
      <!-- Icon -->
      <div class="relative inline-flex items-center justify-center mb-8">
        <div class="w-24 h-24 bg-emerald-500/15 border border-emerald-500/30 rounded-full flex items-center justify-center animate-glow">
          <div class="w-16 h-16 bg-emerald-500 rounded-full flex items-center justify-center shadow-glow">
            <i class="fas fa-check text-white text-2xl"></i>
          </div>
        </div>
        <!-- Floating particles -->
        <div class="absolute -top-2 -right-2 w-4 h-4 bg-emerald-400 rounded-full opacity-60 animate-float" style="animation-delay: 0s"></div>
        <div class="absolute top-0 -left-4 w-3 h-3 bg-cyan-400 rounded-full opacity-50 animate-float" style="animation-delay: 1s"></div>
        <div class="absolute -bottom-1 right-0 w-3 h-3 bg-emerald-300 rounded-full opacity-40 animate-float" style="animation-delay: 2s"></div>
      </div>

      <!-- Content -->
      <h1 class="text-4xl font-black text-white mb-3">Pedido Confirmado!</h1>
      <p class="text-slate-400 text-base leading-relaxed mb-2">
        Obrigado pela sua compra. Seu pedido foi confirmado e será enviado em breve.
      </p>
      <p class="text-emerald-400 text-sm font-medium mb-10">
        <i class="fas fa-envelope mr-1.5"></i>
        Um e-mail de confirmação foi enviado para você.
      </p>

      <!-- Order details card -->
      <div class="card p-5 mb-8 text-left">
        <div class="flex items-center gap-3 mb-4">
          <div class="w-8 h-8 bg-emerald-500/10 border border-emerald-500/20 rounded-lg flex items-center justify-center">
            <i class="fas fa-truck text-emerald-400 text-xs"></i>
          </div>
          <div>
            <p class="text-white text-sm font-semibold">Entrega estimada</p>
            <p class="text-slate-400 text-xs">3-5 dias úteis</p>
          </div>
        </div>
        <div class="flex items-center gap-3">
          <div class="w-8 h-8 bg-emerald-500/10 border border-emerald-500/20 rounded-lg flex items-center justify-center">
            <i class="fas fa-box text-emerald-400 text-xs"></i>
          </div>
          <div>
            <p class="text-white text-sm font-semibold">Status do Pedido</p>
            <span class="badge badge-emerald text-[11px] mt-0.5 inline-flex">
              <span class="w-1.5 h-1.5 bg-emerald-400 rounded-full animate-pulse mr-1"></span>
              Em processamento
            </span>
          </div>
        </div>
      </div>

      <!-- Actions -->
      <div class="space-y-3">
        <router-link to="/" class="btn btn-primary w-full py-3">
          <i class="fas fa-home text-sm"></i>
          Continuar Comprando
        </router-link>
        <button
          @click="downloadReceipt"
          class="btn btn-secondary w-full py-3"
        >
          <i class="fas fa-download text-sm"></i>
          Baixar Recibo
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
const downloadReceipt = () => {
  alert('Funcionalidade de recibo em breve!')
}
</script>
