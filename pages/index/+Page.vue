<template>
  <div class="min-h-screen relative pb-24 font-sans selection:bg-blue-100">
    <!-- 背景层：保持极致模糊感 -->
    <div class="fixed inset-0 z-0">
      <img src="https://api.yppp.net/api.php" class="w-full h-full object-cover" />
      <div class="absolute inset-0 bg-white/30 backdrop-blur-[12px]"></div> 
    </div>

    <div class="relative z-10 max-w-5xl mx-auto px-5 pt-10 space-y-10">
      
      <!-- 顶部公告：大圆角 + 柔和阴影 -->
      <section class="rounded-[32px] border border-white/60 bg-white/40 backdrop-blur-2xl p-7 shadow-xl shadow-blue-900/5">
        <div class="flex items-center gap-3 mb-4">
          <div class="p-2 bg-blue-500 rounded-2xl shadow-lg shadow-blue-500/20">
            <svg xmlns="http://www.w3.org/2000/svg" class="size-5 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
              <path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9"></path>
              <path d="M13.73 21a2 2 0 0 1-3.46 0"></path>
            </svg>
          </div>
          <h2 class="text-slate-800 font-heavy text-xl tracking-tighter">站点公告</h2>
        </div>
        <div 
          v-if="site.notice" 
          class="text-slate-600 font-medium text-sm md:text-base leading-relaxed whitespace-pre-wrap pl-1"
        >
          {{ site.notice }}
        </div>
      </section>

      <!-- 分类标签：药片式设计 -->
      <nav class="flex gap-3 overflow-x-auto no-scrollbar py-2">
        <button
          v-for="cat in [null, ...catalog.categories]"
          :key="cat ? cat.id : 'all'"
          @click="activeCategoryId = cat ? cat.id : null"
          class="px-7 py-3 rounded-[20px] text-sm font-bold transition-all duration-300 whitespace-nowrap border"
          :class="activeCategoryId === (cat ? cat.id : null) 
            ? 'bg-blue-600 text-white border-blue-600 shadow-xl shadow-blue-600/20 scale-105' 
            : 'bg-white/60 text-slate-500 border-white/80 hover:bg-white/90'"
        >
          {{ cat ? cat.name : '全部商品' }}
        </button>
      </nav>

      <!-- 商品列表：参照图 11 的极简白色卡片 -->
      <div v-if="filteredProducts.length" class="grid gap-6 grid-cols-1 sm:grid-cols-2">
        <article 
          v-for="product in filteredProducts" 
          :key="product.id" 
          class="group relative bg-white/80 backdrop-blur-md rounded-[32px] border border-white/60 p-6 flex items-center gap-6 transition-all duration-500 hover:bg-white hover:shadow-2xl hover:shadow-blue-900/10 hover:-translate-y-1.5 overflow-hidden"
        >
          <!-- 侧边装饰条：图 11 的视觉灵魂 -->
          <div class="absolute left-0 top-1/4 bottom-1/4 w-1.5 bg-blue-500 rounded-r-full transform -translate-x-1 group-hover:translate-x-0 transition-transform"></div>

          <!-- 商品封面：圆角正方形 -->
          <div class="size-24 shrink-0 bg-slate-50 rounded-[24px] p-4 flex items-center justify-center shadow-inner group-hover:bg-blue-50/50 transition-colors">
            <img 
              :src="product.coverImage || emptyCoverUrl" 
              class="max-h-full max-w-full object-contain drop-shadow-md group-hover:scale-110 transition-transform duration-500"
            />
          </div>

          <!-- 内容区 -->
          <div class="flex-grow min-w-0 flex flex-col justify-between h-24 py-1">
            <div>
              <h3 class="text-slate-800 font-black text-lg truncate mb-1">{{ product.name }}</h3>
              <p class="text-slate-400 text-xs font-medium truncate">{{ product.subtitle || '点击查看详情' }}</p>
            </div>
            
            <div class="flex items-center justify-between">
              <div class="flex flex-col">
                <div class="flex items-baseline gap-0.5">
                  <span class="text-blue-600 text-sm font-black">¥</span>
                  <span class="text-blue-600 text-2xl font-black">{{ product.price }}</span>
                </div>
                <div class="flex items-center gap-2 mt-1">
                  <span class="size-1.5 rounded-full" :class="product.stock > 0 ? 'bg-emerald-500 animate-pulse' : 'bg-slate-300'"></span>
                  <span class="text-[11px] text-slate-400 font-bold uppercase tracking-widest">
                    STOCK: {{ product.stock || '∞' }}
                  </span>
                </div>
              </div>
              
              <a :href="`/product/${product.slug}`" class="flex items-center justify-center size-12 bg-slate-900 rounded-2xl text-white hover:bg-blue-600 transition-all duration-300 shadow-lg active:scale-90 group/btn">
                <svg xmlns="http://www.w3.org/2000/svg" class="size-5 group-hover:translate-x-0.5 transition-transform" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="3">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M9 5l7 7-7 7" />
                </svg>
              </a>
            </div>
          </div>
        </article>
      </div>

      <!-- 空状态 -->
      <div v-else class="py-40 text-center bg-white/40 backdrop-blur-xl rounded-[48px] border-2 border-dashed border-white/60">
        <div class="text-6xl mb-6 grayscale opacity-50">🛒</div>
        <p class="text-slate-400 font-black text-lg tracking-widest">此分类下暂无商品</p>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from "vue";
import { useData } from "vike-vue/useData";
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

/* 文字渲染优化 */
h2, h3, span, p {
  letter-spacing: -0.02em;
}

/* 极简字体加粗 */
.font-heavy {
  font-weight: 900;
}
</style>
