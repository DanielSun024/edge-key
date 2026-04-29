<template>
  <div class="min-h-screen relative pb-20">
    <div class="fixed inset-0 z-0">
      <img src="https://api.yppp.net/api.php" class="w-full h-full object-cover" />
      <div class="absolute inset-0 bg-black/5 backdrop-brightness-90"></div> 
    </div>

    <div class="relative z-10 max-w-7xl mx-auto px-4 pt-10 space-y-12">
      
      <section class="rounded-[2rem] border border-white/20 bg-white/10 backdrop-blur-2xl p-8 md:p-10 shadow-2xl overflow-hidden relative group">
        <div class="absolute -top-24 -left-24 w-64 h-64 bg-primary/10 rounded-full blur-3xl group-hover:bg-primary/20 transition-all"></div>
        
        <div class="relative z-10">
          <div class="flex items-center gap-3 mb-6 text-white font-black italic tracking-widest">
            <svg xmlns="http://www.w3.org/2000/svg" class="size-6 text-yellow-400 animate-pulse" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M11 5.882V19.24a1.76 1.76 0 01-3.417.592l-2.147-6.15M18 13a3 3 0 100-6M5.436 13.683A4.001 4.001 0 017 6h1.832c4.1 0 7.625-1.234 9.168-3v14c-1.543-1.766-5.067-3-9.168-3H7a3.988 3.988 0 01-1.564-.317z" />
            </svg>
            <span class="text-xl uppercase">公告</span>
          </div>

          <div class="border-t border-dashed border-white/20 mb-8 w-full"></div>

          <div class="space-y-6">
            <div 
              v-if="site.notice" 
              class="text-[#ff7a45] font-bold text-base md:text-xl leading-loose whitespace-pre-wrap drop-shadow-[0_2px_2px_rgba(0,0,0,0.5)]"
            >
              {{ site.notice }}
            </div>
          </div>
        </div>
      </section>

      <div class="flex items-center justify-between px-4">
        <h2 class="text-3xl font-black text-white drop-shadow-lg italic tracking-tighter">精选商品 / Featured</h2>
        
        <div v-if="catalog.categories.length" class="flex gap-2">
          <button
            v-for="cat in [null, ...catalog.categories]"
            :key="cat ? cat.id : 'all'"
            @click="activeCategoryId = cat ? cat.id : null"
            class="px-5 py-2 rounded-xl text-sm font-black transition-all backdrop-blur-md border border-white/10"
            :class="activeCategoryId === (cat ? cat.id : null) 
              ? 'bg-white text-gray-900 shadow-xl' 
              : 'bg-black/30 text-white/80 hover:bg-black/50'"
          >
            {{ cat ? cat.name : '全部' }}
          </button>
        </div>
      </div>

      <div v-if="filteredProducts.length" class="grid gap-8 grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4">
        <article 
          v-for="product in filteredProducts" 
          :key="product.id" 
          class="group relative bg-white/90 backdrop-blur-xl rounded-[2.5rem] border border-white/50 overflow-hidden shadow-xl hover:shadow-[0_20px_50px_rgba(0,0,0,0.3)] transition-all duration-500 hover:-translate-y-2 flex flex-col"
        >
          <a :href="`/product/${product.slug}`" class="block aspect-square p-6 relative overflow-hidden">
            <img 
              :src="product.coverImage || emptyCoverUrl" 
              class="h-full w-full object-contain transition-transform duration-700 group-hover:scale-110"
            />
            <div class="absolute inset-0 bg-gradient-to-t from-white/20 to-transparent opacity-0 group-hover:opacity-100 transition-opacity"></div>
          </a>

          <div class="p-8 pt-0 flex flex-col items-center text-center">
            <span class="text-xs text-gray-400 font-bold uppercase tracking-widest mb-2">{{ product.categoryName || 'Common' }}</span>
            <h3 class="text-xl font-black text-gray-800 mb-6 line-clamp-1 italic tracking-tight">{{ product.name }}</h3>

            <div class="w-full flex items-center justify-between bg-gray-50 p-4 rounded-3xl border border-gray-100">
              <div class="flex items-baseline text-red-500 font-black italic">
                <span class="text-sm mr-0.5">¥</span>
                <span class="text-3xl tracking-tighter">{{ formatCents(product.price) }}</span>
              </div>
              <a :href="`/product/${product.slug}`" class="btn btn-primary btn-md rounded-2xl px-6 font-bold shadow-lg shadow-primary/20 transition-transform active:scale-95">
                详情
              </a>
            </div>
          </div>
        </article>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from "vue";
import { useData } from "vike-vue/useData";
import { formatCents } from "../../lib/utils/money";
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
/* 保持单行省略 */
.line-clamp-1 {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 1;
  overflow: hidden;
}

/* 提高磨砂模糊强度，对应参考图效果 */
.backdrop-blur-2xl {
  backdrop-filter: blur(40px);
  -webkit-backdrop-filter: blur(40px);
}
</style>
