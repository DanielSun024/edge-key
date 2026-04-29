<template>
  <div class="min-h-screen relative pb-20">
    <div class="fixed inset-0 z-0">
      <img src="https://api.yppp.net/api.php" class="w-full h-full object-cover" />
      <div class="absolute inset-0 bg-black/10"></div> </div>

    <div class="relative z-10 max-w-7xl mx-auto px-4 pt-6 space-y-8">
      
      <section class="rounded-3xl border border-white/30 bg-white/40 backdrop-blur-xl p-6 md:p-8 shadow-2xl">
        <div class="flex items-center gap-2 mb-4 text-base-content font-bold">
          <svg xmlns="http://www.w3.org/2000/svg" class="size-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5.882V19.24a1.76 1.76 0 01-3.417.592l-2.147-6.15M18 13a3 3 0 100-6M5.436 13.683A4.001 4.001 0 017 6h1.832c4.1 0 7.625-1.234 9.168-3v14c-1.543-1.766-5.067-3-9.168-3H7a3.988 3.988 0 01-1.564-.317z" />
          </svg>
          <span>公告</span>
        </div>

        <div class="border-t border-dashed border-white/40 my-4"></div>

        <div class="space-y-4">
          <div 
            v-if="site.notice" 
            class="text-orange-600 font-medium text-base md:text-lg leading-relaxed whitespace-pre-wrap drop-shadow-sm"
          >
            {{ site.notice }}
          </div>
        </div>
      </section>

      <div class="flex items-center justify-between px-2">
        <h2 class="text-2xl font-black text-white drop-shadow-md tracking-wider">精选商品</h2>
        
        <div v-if="catalog.categories.length" class="flex gap-2">
          <button
            v-for="cat in [null, ...catalog.categories]"
            :key="cat ? cat.id : 'all'"
            @click="activeCategoryId = cat ? cat.id : null"
            class="px-4 py-1.5 rounded-full text-sm font-bold transition-all backdrop-blur-md border"
            :class="activeCategoryId === (cat ? cat.id : null) 
              ? 'bg-white text-primary border-white' 
              : 'bg-black/20 text-white border-white/20 hover:bg-black/40'"
          >
            {{ cat ? cat.name : '全部' }}
          </button>
        </div>
      </div>

      <div v-if="filteredProducts.length" class="grid gap-6 grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4">
        <article 
          v-for="product in filteredProducts" 
          :key="product.id" 
          class="group relative bg-white/80 backdrop-blur-md rounded-3xl border border-white/50 overflow-hidden shadow-lg hover:shadow-2xl transition-all duration-300 hover:-translate-y-1"
        >
          <a :href="`/product/${product.slug}`" class="block aspect-square p-4">
            <img 
              :src="product.coverImage || emptyCoverUrl" 
              class="h-full w-full object-contain mix-blend-multiply transition-transform duration-500 group-hover:scale-110"
            />
          </a>

          <div class="p-6 pt-0 flex flex-col items-center text-center">
            <span class="text-[10px] text-base-content/40 font-bold uppercase mb-1">{{ product.categoryName }}</span>
            <h3 class="text-lg font-black text-base-content mb-4 line-clamp-1 italic">{{ product.name }}</h3>

            <div class="w-full flex items-center justify-between bg-base-100/50 p-3 rounded-2xl border border-white">
              <div class="flex items-baseline text-red-500 font-black italic">
                <span class="text-xs mr-0.5 font-bold">¥</span>
                <span class="text-2xl tracking-tighter">{{ formatCents(product.price) }}</span>
              </div>
              <a :href="`/product/${product.slug}`" class="btn btn-primary btn-sm rounded-xl px-5 shadow-sm">
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
/* 强制单行省略 */
.line-clamp-1 {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 1;
  overflow: hidden;
}

/* 增强磨砂感 */
.backdrop-blur-xl {
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
}
</style>
