<template>
  <div class="min-h-screen relative pb-20">
    <div class="fixed inset-0 z-0">
      <img src="https://api.yppp.net/api.php" class="w-full h-full object-cover" />
      <div class="absolute inset-0 bg-black/5 backdrop-blur-[2px]"></div> 
    </div>

    <div class="relative z-10 max-w-6xl mx-auto px-4 pt-6 space-y-6">
      
      <section class="rounded-2xl border border-white/30 bg-white/10 backdrop-blur-xl p-5 md:p-6 shadow-xl relative overflow-hidden">
        <div class="flex items-center gap-2 mb-3 text-white/90 font-bold text-sm tracking-widest">
          <svg xmlns="http://www.w3.org/2000/svg" class="size-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5.882V19.24a1.76 1.76 0 01-3.417.592l-2.147-6.15M18 13a3 3 0 100-6M5.436 13.683A4.001 4.001 0 017 6h1.832c4.1 0 7.625-1.234 9.168-3v14c-1.543-1.766-5.067-3-9.168-3H7a3.988 3.988 0 01-1.564-.317z" />
          </svg>
          <span>公告</span>
        </div>

        <div class="border-t border-dashed border-white/20 mb-4"></div>

        <div 
          v-if="site.notice" 
          class="text-orange-500 font-bold text-sm md:text-base leading-relaxed whitespace-pre-wrap drop-shadow-md"
        >
          {{ site.notice }}
        </div>
      </section>

      <section class="rounded-2xl border border-white/30 bg-white/10 backdrop-blur-lg p-3 flex items-center gap-3">
        <div class="flex items-center gap-2 px-3 border-r border-white/20 mr-2 text-white/80 text-xs font-bold shrink-0">
          <svg xmlns="http://www.w3.org/2000/svg" class="size-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7" />
          </svg>
          选择分类
        </div>
        <div class="flex gap-2 overflow-x-auto no-scrollbar">
          <button
            v-for="cat in [null, ...catalog.categories]"
            :key="cat ? cat.id : 'all'"
            @click="activeCategoryId = cat ? cat.id : null"
            class="px-4 py-1.5 rounded-xl text-xs font-bold transition-all whitespace-nowrap"
            :class="activeCategoryId === (cat ? cat.id : null) 
              ? 'bg-white/90 text-primary shadow-inner' 
              : 'bg-white/5 text-white/70 hover:bg-white/20'"
          >
            {{ cat ? cat.name : '全部商品' }}
          </button>
        </div>
      </section>

      <div v-if="filteredProducts.length" class="grid gap-4 grid-cols-1 sm:grid-cols-2 lg:grid-cols-3">
        <article 
          v-for="product in filteredProducts" 
          :key="product.id" 
          class="group relative bg-white/40 backdrop-blur-md rounded-2xl border border-white/40 p-4 flex items-center gap-4 hover:bg-white/60 transition-all duration-300 shadow-sm hover:shadow-lg"
        >
          <div class="size-16 shrink-0 bg-white/50 rounded-xl p-2 flex items-center justify-center overflow-hidden">
            <img 
              :src="product.coverImage || emptyCoverUrl" 
              class="max-h-full max-w-full object-contain transition-transform group-hover:scale-110"
            />
          </div>

          <div class="flex-grow min-w-0">
            <h3 class="text-gray-800 font-bold text-base truncate mb-1">{{ product.name }}</h3>
            <div class="flex items-center justify-between">
              <div class="flex flex-col">
                <span class="text-red-500 font-black text-lg">
                  <span class="text-xs font-bold">¥</span>{{ formatCents(product.price) }}
                </span>
                <span class="text-[10px] text-gray-400 font-medium">库存: {{ product.stock || '未知' }}</span>
              </div>
              
              <a :href="`/product/${product.slug}`" class="btn btn-primary btn-xs rounded-lg px-4 no-animation shadow-sm">
                详情
              </a>
            </div>
          </div>

          <div class="absolute bottom-0 left-4 right-4 h-0.5 bg-gray-200/30 rounded-full overflow-hidden">
            <div class="h-full bg-success w-2/3"></div>
          </div>
        </article>
      </div>

      <div v-else class="py-20 text-center bg-white/10 backdrop-blur-md rounded-3xl border border-dashed border-white/30 text-white/40">
        暂无在售商品
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
/* 隐藏滚动条但保留滚动功能 */
.no-scrollbar::-webkit-scrollbar {
  display: none;
}
.no-scrollbar {
  -ms-overflow-style: none;
  scrollbar-width: none;
}

/* 调整卡片微动效 */
.group:hover .btn-primary {
  transform: translateY(-1px);
}
</style>
