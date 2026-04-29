<template>
  <div class="min-h-screen relative pb-20 font-sans selection:bg-white/30">
    <!-- 背景层：固定全屏，确保图片完全可见 -->
    <div class="fixed inset-0 z-0">
      <img src="https://api.yppp.net/api.php" class="w-full h-full object-cover" />
      <!-- 极轻微的暗化遮罩，增强白色文字对比度，同时保持背景清晰 -->
      <div class="absolute inset-0 bg-black/10 backdrop-blur-[1px]"></div> 
    </div>

    <div class="relative z-10 max-w-6xl mx-auto px-4 pt-8 space-y-8">
      
      <!-- 公告部分：极简通透设计 -->
      <section class="rounded-3xl border border-white/20 bg-white/5 backdrop-blur-2xl p-6 shadow-2xl relative overflow-hidden group">
        <div class="flex items-center gap-3 mb-4">
          <div class="p-2 bg-white/10 rounded-xl">
            <svg xmlns="http://www.w3.org/2000/svg" class="size-5 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5.882V19.24a1.76 1.76 0 01-3.417.592l-2.147-6.15M18 13a3 3 0 100-6M5.436 13.683A4.001 4.001 0 017 6h1.832c4.1 0 7.625-1.234 9.168-3v14c-1.543-1.766-5.067-3-9.168-3H7a3.988 3.988 0 01-1.564-.317z" />
            </svg>
          </div>
          <span class="text-white font-bold text-lg tracking-[0.2em] drop-shadow-md">公告通知</span>
        </div>

        <div class="w-full h-px bg-gradient-to-r from-transparent via-white/20 to-transparent mb-4"></div>

        <div 
          v-if="site.notice" 
          class="text-white/90 font-medium text-sm md:text-base leading-relaxed whitespace-pre-wrap drop-shadow-[0_2px_4px_rgba(0,0,0,0.5)]"
        >
          {{ site.notice }}
        </div>
      </section>

      <!-- 分类切换：胶囊悬浮效果 -->
      <section class="rounded-2xl border border-white/10 bg-black/10 backdrop-blur-md p-2 flex items-center gap-2 overflow-hidden">
        <div class="flex items-center gap-2 px-4 py-1 border-r border-white/10 text-white/60 text-xs font-bold shrink-0">
          <svg xmlns="http://www.w3.org/2000/svg" class="size-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7" />
          </svg>
          分类
        </div>
        <div class="flex gap-2 overflow-x-auto no-scrollbar py-1 px-2">
          <button
            v-for="cat in [null, ...catalog.categories]"
            :key="cat ? cat.id : 'all'"
            @click="activeCategoryId = cat ? cat.id : null"
            class="px-5 py-2 rounded-xl text-xs font-bold transition-all duration-300 whitespace-nowrap"
            :class="activeCategoryId === (cat ? cat.id : null) 
              ? 'bg-white text-gray-900 shadow-lg scale-105' 
              : 'bg-white/5 text-white/70 hover:bg-white/20 hover:text-white'"
          >
            {{ cat ? cat.name : '全部商品' }}
          </button>
        </div>
      </section>

      <!-- 商品列表：毛玻璃卡片 -->
      <div v-if="filteredProducts.length" class="grid gap-6 grid-cols-1 sm:grid-cols-2 lg:grid-cols-3">
        <article 
          v-for="product in filteredProducts" 
          :key="product.id" 
          class="group relative bg-white/[0.08] backdrop-blur-xl rounded-3xl border border-white/20 p-5 flex items-center gap-5 hover:bg-white/[0.15] hover:border-white/40 hover:-translate-y-1 transition-all duration-500 shadow-lg"
        >
          <!-- 图片容器 -->
          <div class="size-20 shrink-0 bg-white/10 rounded-2xl p-2 flex items-center justify-center overflow-hidden border border-white/10 group-hover:border-white/30 transition-colors">
            <img 
              :src="product.coverImage || emptyCoverUrl" 
              class="max-h-full max-w-full object-contain transition-transform duration-500 group-hover:scale-115"
              loading="lazy"
            />
          </div>

          <!-- 内容容器 -->
          <div class="flex-grow min-w-0">
            <h3 class="text-white font-bold text-base truncate mb-1.5 drop-shadow-[0_2px_2px_rgba(0,0,0,0.3)]">
              {{ product.name }}
            </h3>
            <div class="flex items-end justify-between">
              <div class="flex flex-col">
                <span class="text-white font-black text-xl flex items-baseline gap-0.5">
                  <span class="text-xs font-bold opacity-80">¥</span>
                  <span class="drop-shadow-md">{{ formatCents(product.price) }}</span>
                </span>
                <span class="text-[10px] text-white/50 font-medium mt-1 uppercase tracking-wider">Stock: {{ product.stock || '0' }}</span>
              </div>
              
              <a 
                :href="`/product/${product.slug}`" 
                class="bg-white/10 hover:bg-white text-white hover:text-gray-900 px-4 py-2 rounded-xl text-xs font-bold border border-white/20 transition-all duration-300 backdrop-blur-sm"
              >
                购买
              </a>
            </div>
          </div>

          <!-- 底部装饰条：更精致的进度感 -->
          <div class="absolute bottom-3 left-6 right-6 h-[2px] bg-white/5 rounded-full overflow-hidden">
            <div class="h-full bg-gradient-to-r from-blue-400 to-indigo-400 w-1/3 opacity-50 group-hover:opacity-100 transition-opacity"></div>
          </div>
        </article>
      </div>

      <!-- 空状态 -->
      <div v-else class="py-32 text-center bg-white/5 backdrop-blur-md rounded-[3rem] border border-dashed border-white/20 text-white/30 flex flex-col items-center gap-4">
        <svg xmlns="http://www.w3.org/2000/svg" class="size-12 opacity-20" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />
        </svg>
        <span class="tracking-[0.3em] font-light">暂无在售商品</span>
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

/* 简单的进场动画 */
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

article {
  animation: fadeIn 0.5s ease-out forwards;
}
</style>
