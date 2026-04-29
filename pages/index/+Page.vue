<template>
  <div class="min-h-screen relative pb-20 font-sans">
    <div class="fixed inset-0 z-0">
      <img src="https://api.yppp.net/api.php" class="w-full h-full object-cover" />
      <div class="absolute inset-0 bg-slate-900/20 backdrop-blur-[8px]"></div> 
    </div>

    <div class="relative z-10 max-w-6xl mx-auto px-4 pt-8 space-y-8">
      
      <section class="rounded-3xl border border-white/20 bg-white/10 backdrop-blur-2xl p-6 md:p-8 shadow-2xl relative overflow-hidden">
        <div class="flex items-center gap-3 mb-4 text-white/90 font-bold text-lg tracking-widest">
          <svg xmlns="http://www.w3.org/2000/svg" class="size-6 text-orange-300" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5.882V19.24a1.76 1.76 0 01-3.417.592l-2.147-6.15M18 13a3 3 0 100-6M5.436 13.683A4.001 4.001 0 017 6h1.832c4.1 0 7.625-1.234 9.168-3v14c-1.543-1.766-5.067-3-9.168-3H7a3.988 3.988 0 01-1.564-.317z" />
          </svg>
          <span>官方公告</span>
        </div>

        <div class="border-t border-white/10 mb-5"></div>

        <div 
          v-if="site.notice" 
          class="text-orange-100/90 font-semibold text-sm md:text-base leading-relaxed whitespace-pre-wrap drop-shadow-[0_2px_4px_rgba(0,0,0,0.5)]"
        >
          {{ site.notice }}
        </div>
      </section>

      <section class="rounded-2xl border border-white/20 bg-white/5 backdrop-blur-xl p-2 flex items-center gap-4">
        <div class="flex items-center gap-2 px-4 py-2 border-r border-white/10 text-white/60 text-xs font-bold shrink-0">
          <svg xmlns="http://www.w3.org/2000/svg" class="size-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7" />
          </svg>
          选择分类
        </div>
        <div class="flex gap-2 overflow-x-auto no-scrollbar py-1">
          <button
            v-for="cat in [null, ...catalog.categories]"
            :key="cat ? cat.id : 'all'"
            @click="activeCategoryId = cat ? cat.id : null"
            class="px-5 py-2 rounded-xl text-xs font-bold transition-all whitespace-nowrap"
            :class="activeCategoryId === (cat ? cat.id : null) 
              ? 'bg-blue-500 text-white shadow-lg shadow-blue-500/30 scale-105' 
              : 'bg-white/5 text-white/70 hover:bg-white/10'"
          >
            {{ cat ? cat.name : '全部商品' }}
          </button>
        </div>
      </section>

      <div v-if="filteredProducts.length" class="grid gap-6 grid-cols-1 sm:grid-cols-2 lg:grid-cols-3">
        <article 
          v-for="product in filteredProducts" 
          :key="product.id" 
          class="group relative bg-white/70 backdrop-blur-xl rounded-3xl border border-white/50 p-5 flex items-center gap-5 hover:bg-white/90 transition-all duration-500 shadow-xl hover:-translate-y-1"
        >
          <div class="size-20 shrink-0 bg-slate-100 rounded-2xl p-3 flex items-center justify-center overflow-hidden shadow-inner">
            <img 
              :src="product.coverImage || emptyCoverUrl" 
              class="max-h-full max-w-full object-contain transition-transform duration-500 group-hover:scale-110"
            />
          </div>

          <div class="flex-grow min-w-0 space-y-2">
            <h3 class="text-slate-800 font-extrabold text-base truncate group-hover:text-blue-600 transition-colors">
              {{ product.name }}
            </h3>
            
            <div class="flex items-end justify-between">
              <div class="flex flex-col">
                <span class="text-rose-500 font-black text-xl">
                  <span class="text-xs mr-0.5">¥</span>{{ product.price }}
                </span>
                <span class="text-[10px] text-slate-400 font-bold tracking-tight">
                  库存: <span :class="product.stock > 0 ? 'text-emerald-500' : 'text-rose-400'">{{ product.stock || '充足' }}</span>
                </span>
              </div>
              
              <a :href="`/product/${product.slug}`" class="px-5 py-2 bg-blue-600 hover:bg-blue-700 text-white text-xs font-bold rounded-xl transition-all shadow-md active:scale-95">
                详情
              </a>
            </div>
          </div>

          <div class="absolute bottom-0 left-6 right-6 h-1 bg-slate-100 rounded-full overflow-hidden opacity-50">
            <div 
              class="h-full transition-all duration-1000" 
              :class="product.stock > 10 ? 'bg-emerald-400 w-full' : 'bg-rose-400 w-1/3'"
            ></div>
          </div>
        </article>
      </div>

      <div v-else class="py-32 text-center bg-white/5 backdrop-blur-md rounded-[40px] border border-dashed border-white/20 text-white/30">
        <div class="text-5xl mb-4">📦</div>
        <p class="font-bold tracking-widest">暂无在售商品</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from "vue";
import { useData } from "vike-vue/useData";
// 移除了 formatCents，因为数据库已改为“元”单位
import emptyCoverUrl from "../../assets/empty.jpg";
import type { Data } from "./+data";

const { site, catalog } = useData<Data>();
const activeCategoryId = ref<number | null>(null);

const filteredProducts = computed(() => {
  if (activeCategoryId.value === null) return catalog.products;
  return catalog.products.filter((p) => p.categoryId === activeCategoryId.value);
});
</script>

<style scoped>
.no-scrollbar::-webkit-scrollbar {
  display: none;
}
.no-scrollbar {
  -ms-overflow-style: none;
  scrollbar-width: none;
}

/* 增强字体渲染 */
:deep(*) {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
