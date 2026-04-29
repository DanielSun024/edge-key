<template>
  <div class="min-h-screen relative pb-20 font-sans selection:bg-white/30">
    <!-- 背景层：固定全屏 -->
    <div class="fixed inset-0 z-0">
      <img src="https://api.yppp.net/api.php" class="w-full h-full object-cover" />
      <!-- 极轻微的磨砂感，参考截图中的通透度 -->
      <div class="absolute inset-0 bg-white/5 backdrop-blur-[2px]"></div> 
    </div>

    <div class="relative z-10 max-w-6xl mx-auto px-4 pt-10 space-y-8">
      
      <!-- 公告部分：超高透明度白色容器 -->
      <section class="rounded-2xl border border-white/20 bg-white/40 backdrop-blur-md p-6 shadow-sm relative overflow-hidden group">
        <div class="flex items-center gap-3 mb-4">
          <div class="flex items-center gap-2 text-gray-800/80 font-bold">
            <svg xmlns="http://www.w3.org/2000/svg" class="size-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5.882V19.24a1.76 1.76 0 01-3.417.592l-2.147-6.15M18 13a3 3 0 100-6M5.436 13.683A4.001 4.001 0 017 6h1.832c4.1 0 7.625-1.234 9.168-3v14c-1.543-1.766-5.067-3-9.168-3H7a3.988 3.988 0 01-1.564-.317z" />
            </svg>
            <span class="tracking-widest">公告</span>
          </div>
        </div>

        <!-- 极细的虚线分割线 -->
        <div class="border-t border-dashed border-gray-400/20 mb-5"></div>

        <div 
          v-if="site.notice" 
          class="text-orange-600 font-bold text-sm md:text-[15px] leading-relaxed whitespace-pre-wrap"
        >
          {{ site.notice }}
        </div>
      </section>

      <!-- 分类切换：参考截图的浅白色背景 -->
      <section class="rounded-xl border border-white/30 bg-white/40 backdrop-blur-sm p-3 flex items-center gap-3">
        <div class="flex items-center gap-2 px-3 border-r border-gray-300/30 mr-2 text-gray-700/70 text-xs font-bold shrink-0">
          <svg xmlns="http://www.w3.org/2000/svg" class="size-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" opacity="0.6">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7" />
          </svg>
          选择分类
        </div>
        <div class="flex gap-2 overflow-x-auto no-scrollbar">
          <button
            v-for="cat in [null, ...catalog.categories]"
            :key="cat ? cat.id : 'all'"
            @click="activeCategoryId = cat ? cat.id : null"
            class="px-4 py-1.5 rounded-lg text-xs font-bold transition-all duration-300 whitespace-nowrap"
            :class="activeCategoryId === (cat ? cat.id : null) 
              ? 'bg-white text-blue-500 shadow-sm border border-blue-100' 
              : 'bg-white/30 text-gray-600 hover:bg-white/60'"
          >
            {{ cat ? cat.name : '全部商品' }}
          </button>
        </div>
      </section>

      <!-- 商品列表：参考截图的大圆角浅白卡片 -->
      <div v-if="filteredProducts.length" class="grid gap-4 grid-cols-1 sm:grid-cols-2 lg:grid-cols-3">
        <article 
          v-for="product in filteredProducts" 
          :key="product.id" 
          class="group relative bg-white/50 backdrop-blur-md rounded-2xl border border-white/40 p-4 flex items-center gap-4 hover:bg-white/70 transition-all duration-300 shadow-sm"
        >
          <!-- 图片容器 -->
          <div class="size-16 shrink-0 bg-white/40 rounded-xl p-2 flex items-center justify-center overflow-hidden border border-white/20">
            <img 
              :src="product.coverImage || emptyCoverUrl" 
              class="max-h-full max-w-full object-contain grayscale-[0.2] group-hover:grayscale-0 transition-all"
              loading="lazy"
            />
          </div>

          <!-- 内容容器 -->
          <div class="flex-grow min-w-0">
            <h3 class="text-gray-700 font-bold text-sm truncate mb-1">
              {{ product.name }}
            </h3>
            <div class="flex items-center justify-between">
              <div class="flex flex-col">
                <span class="text-emerald-500 font-black text-base">
                  <span class="text-[10px] font-bold">¥</span>
                  <span>{{ formatCents(product.price) }}</span>
                </span>
                <span class="text-[10px] text-gray-400 font-medium">库存: {{ product.stock || '未知' }}</span>
              </div>
              
              <a 
                :href="`/product/${product.slug}`" 
                class="hidden group-hover:flex items-center justify-center bg-blue-500 text-white text-[10px] px-3 py-1 rounded-md font-bold shadow-sm"
              >
                详情
              </a>
            </div>
          </div>

          <!-- 底部进度条式装饰：参考截图中的绿色线条 -->
          <div class="absolute bottom-0 left-4 right-4 h-0.5 bg-gray-200/20 rounded-full overflow-hidden">
            <div class="h-full bg-emerald-400 w-1/2 opacity-70"></div>
          </div>
        </article>
      </div>

      <!-- 空状态 -->
      <div v-else class="py-24 text-center bg-white/30 backdrop-blur-sm rounded-3xl border border-dashed border-white/40 text-gray-400/60 flex flex-col items-center gap-4">
        <span class="tracking-widest text-sm font-medium">暂无商品数据</span>
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
.no-scrollbar::-webkit-scrollbar {
  display: none;
}
.no-scrollbar {
  -ms-overflow-style: none;
  scrollbar-width: none;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

article {
  animation: fadeIn 0.4s ease-in-out forwards;
}
</style>
