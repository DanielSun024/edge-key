<template>
  <div class="min-h-screen relative pb-24 font-sans selection:bg-blue-500/30">
    <!-- 背景层：深色调幻彩背景 -->
    <div class="fixed inset-0 z-0">
      <img src="https://api.yppp.net/api.php" class="w-full h-full object-cover" />
      <div class="absolute inset-0 bg-slate-950/40 backdrop-blur-[15px]"></div> 
      <div class="absolute inset-0 bg-gradient-to-br from-blue-600/10 via-transparent to-purple-600/10"></div>
    </div>

    <div class="relative z-10 max-w-5xl mx-auto px-5 pt-12 space-y-10">
      
      <!-- 顶部公告：深色玻璃拟态 -->
      <section class="rounded-[32px] border border-white/10 bg-slate-900/40 backdrop-blur-3xl p-7 shadow-2xl shadow-black/20">
        <div class="flex items-center gap-3 mb-4">
          <div class="p-2.5 bg-gradient-to-br from-blue-500 to-blue-600 rounded-2xl shadow-lg shadow-blue-500/30">
            <svg xmlns="http://www.w3.org/2000/svg" class="size-5 text-white" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
              <path d="M18 8A6 6 0 0 0 6 8c0 7-3 9-3 9h18s-3-2-3-9"></path>
              <path d="M13.73 21a2 2 0 0 1-3.46 0"></path>
            </svg>
          </div>
          <h2 class="text-white font-black text-xl tracking-tight">官方通知</h2>
        </div>
        <div 
          v-if="site.notice" 
          class="text-slate-300 font-medium text-sm md:text-base leading-relaxed whitespace-pre-wrap pl-1 opacity-90"
        >
          {{ site.notice }}
        </div>
      </section>

      <!-- 分类标签：深色胶囊设计（已去掉“全部商品”） -->
      <nav v-if="catalog.categories.length" class="flex gap-3 overflow-x-auto no-scrollbar py-2">
        <button
          v-for="cat in catalog.categories"
          :key="cat.id"
          @click="activeCategoryId = cat.id"
          class="px-8 py-3 rounded-[20px] text-sm font-bold transition-all duration-300 whitespace-nowrap border"
          :class="activeCategoryId === cat.id 
            ? 'bg-blue-600 text-white border-blue-400 shadow-xl shadow-blue-600/40 scale-105' 
            : 'bg-slate-900/50 text-slate-400 border-white/5 hover:bg-slate-800/80 hover:text-white'"
        >
          {{ cat.name }}
        </button>
      </nav>

      <!-- 商品列表 -->
      <div v-if="filteredProducts.length" class="grid gap-6 grid-cols-1 sm:grid-cols-2">
        <article 
          v-for="product in filteredProducts" 
          :key="product.id" 
          class="group relative bg-slate-900/40 backdrop-blur-2xl rounded-[32px] border border-white/10 p-6 flex items-center gap-6 transition-all duration-500 hover:bg-slate-800/60 hover:shadow-2xl hover:shadow-blue-500/10 hover:-translate-y-1.5 overflow-hidden"
        >
          <!-- 侧边高亮装饰 -->
          <div class="absolute left-0 top-6 bottom-6 w-1 bg-blue-500 rounded-r-full shadow-[0_0_15px_rgba(59,130,246,0.8)] transform -translate-x-1 group-hover:translate-x-0 transition-transform duration-500"></div>

          <!-- 商品封面 -->
          <div class="size-24 shrink-0 bg-slate-950/50 rounded-[24px] p-4 flex items-center justify-center border border-white/5 shadow-inner group-hover:border-blue-500/30 transition-colors">
            <img 
              :src="product.coverImage || emptyCoverUrl" 
              class="max-h-full max-w-full object-contain drop-shadow-2xl group-hover:scale-110 transition-transform duration-700"
            />
          </div>

          <!-- 内容区 -->
          <div class="flex-grow min-w-0 flex flex-col justify-between h-24 py-1">
            <div>
              <h3 class="text-white font-black text-lg truncate mb-1 group-hover:text-blue-400 transition-colors">{{ product.name }}</h3>
              <div class="flex items-center gap-2">
                <span class="text-[10px] px-2 py-0.5 rounded-md bg-blue-500/10 text-blue-400 font-bold border border-blue-500/20">正品保障</span>
                <p class="text-slate-500 text-xs font-medium truncate max-w-[100px]">{{ product.subtitle || '精品推荐' }}</p>
              </div>
            </div>
            
            <div class="flex items-center justify-between">
              <div class="flex flex-col">
                <div class="flex items-baseline gap-0.5">
                  <span class="text-blue-400 text-sm font-black">¥</span>
                  <span class="text-white text-2xl font-black tracking-tighter">{{ product.price }}</span>
                </div>
                <div class="flex items-center gap-1.5 mt-1">
                  <div class="size-1.5 rounded-full bg-emerald-500 shadow-[0_0_8px_rgba(16,185,129,0.6)]"></div>
                  <span class="text-[10px] text-slate-500 font-bold uppercase tracking-tighter">
                    STOCK: {{ product.stock || '999+' }}
                  </span>
                </div>
              </div>
              
              <!-- 交互按钮 -->
              <a :href="`/product/${product.slug}`" class="relative overflow-hidden flex items-center justify-center px-6 py-3 bg-blue-600 rounded-2xl text-white text-xs font-black hover:bg-blue-500 transition-all duration-300 shadow-lg shadow-blue-600/20 active:scale-95 group/btn">
                <span class="relative z-10">立即选购</span>
                <div class="absolute inset-0 bg-gradient-to-r from-transparent via-white/20 to-transparent -translate-x-full group-hover/btn:animate-[shimmer_1.5s_infinite]"></div>
              </a>
            </div>
          </div>
        </article>
      </div>

      <!-- 空状态 -->
      <div v-else class="py-40 text-center bg-slate-900/20 backdrop-blur-xl rounded-[48px] border-2 border-dashed border-white/5">
        <div class="text-6xl mb-6 opacity-20">🪐</div>
        <p class="text-slate-500 font-black text-lg tracking-widest">暂无商品上架</p>
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

// 默认选中第一个分类，如果没有分类则为 null
const activeCategoryId = ref<number | null>(
  catalog.categories.length > 0 ? catalog.categories[0].id : null
);

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

@keyframes shimmer {
  100% {
    transform: translateX(100%);
  }
}

/* 文字平滑优化 */
h2, h3, span, p {
  letter-spacing: -0.01em;
  -webkit-font-smoothing: antialiased;
}
</style>
