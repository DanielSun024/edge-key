<template>
  <div class="max-w-7xl mx-auto px-4 py-8 space-y-12 bg-base-50">
    
<header class="relative rounded-2xl bg-white border border-base-200 p-8 md:p-12 overflow-hidden shadow-sm">
      <div class="relative z-10 flex flex-col lg:flex-row lg:justify-between lg:items-start gap-8">
        
        <div class="flex-grow max-w-3xl space-y-6">
          <div class="space-y-2">
            <h1 class="text-3xl md:text-4xl font-extrabold text-base-content tracking-tight">
              {{ site.siteSubtitle || '公告' }}
            </h1>
            <div class="h-1.5 w-20 bg-primary rounded-full"></div>
          </div>

          <div 
            v-if="site.notice" 
            class="text-base-content/70 text-base md:text-lg leading-relaxed whitespace-pre-wrap"
          >
            {{ site.notice }}
          </div>
        </div>

        <div class="shrink-0">
          <a 
            href="https://t.me/your_link" 
            target="_blank" 
            class="btn btn-primary btn-md md:btn-lg rounded-2xl gap-3 no-animation shadow-lg shadow-primary/20 hover:shadow-primary/40 transition-all"
          >
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
              <path stroke-linecap="round" stroke-linejoin="round" d="M8.625 12a.375.375 0 1 1-.75 0 .375.375 0 0 1 .75 0Zm0 0H8.25m4.125 0a.375.375 0 1 1-.75 0 .375.375 0 0 1 .75 0Zm0 0H12m4.125 0a.375.375 0 1 1-.75 0 .375.375 0 0 1 .75 0Zm0 0h-.375M21 12c0 4.556-4.03 8.25-9 8.25a9.764 9.764 0 0 1-2.555-.337A5.972 5.972 0 0 1 5.41 20.97a5.969 5.969 0 0 1-.474-.065 4.48 4.48 0 0 0 .978-2.025c.09-.457-.133-.901-.467-1.226C3.93 16.178 3 14.189 3 12c0-4.556 4.03-8.25 9-8.25s9 3.694 9 8.25Z" />
            </svg>
            <span class="font-bold">联系在线客服</span>
          </a>
        </div>
      </div>
      
      <div class="absolute -top-24 -right-24 w-96 h-96 bg-primary/5 rounded-full blur-3xl"></div>
    </header>

    <section class="space-y-6">
      <div class="flex flex-col md:flex-row md:items-center justify-between gap-4">
        <h2 class="text-2xl font-bold text-base-content">精选商品</h2>
        
        <div v-if="catalog.categories.length" class="flex flex-wrap gap-2">
          <button
            class="btn btn-sm rounded-full no-animation"
            :class="activeCategoryId === null ? 'btn-primary' : 'btn-ghost bg-white border-base-200'"
            @click="activeCategoryId = null"
          >
            全部
          </button>
          <button
            v-for="category in catalog.categories"
            :key="category.id"
            class="btn btn-sm rounded-full no-animation"
            :class="activeCategoryId === category.id ? 'btn-primary' : 'btn-ghost bg-white border-base-200'"
            @click="activeCategoryId = category.id"
          >
            {{ category.name }}
          </button>
        </div>
      </div>

      <div v-if="filteredProducts.length" class="grid gap-6 grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4">
        <article 
          v-for="product in filteredProducts" 
          :key="product.id" 
          class="group flex flex-col bg-white rounded-xl border border-base-200 overflow-hidden transition-all duration-300 hover:shadow-xl hover:border-primary/30"
        >
          <a :href="`/product/${product.slug}`" class="block aspect-[4/3] overflow-hidden bg-base-100">
            <img 
              :src="product.coverImage || emptyCoverUrl" 
              class="h-full w-full object-cover transition-transform duration-500 group-hover:scale-105"
              :alt="product.name"
            />
          </a>

          <div class="flex flex-col p-5 flex-grow">
            <div class="flex justify-between items-start mb-2">
              <span class="text-[10px] px-2 py-0.5 rounded bg-base-100 text-base-content/50 font-bold uppercase tracking-wider">
                {{ product.categoryName || '未分类' }}
              </span>
            </div>
            
            <h3 class="text-lg font-bold text-base-content mb-4 line-clamp-1 group-hover:text-primary transition-colors">
              {{ product.name }}
            </h3>

            <div class="mt-auto flex items-center justify-between pt-4 border-t border-base-100">
              <div class="flex items-baseline text-error font-black">
                <span class="text-xs mr-0.5">¥</span>
                <span class="text-2xl tracking-tighter">{{ formatCents(product.price) }}</span>
              </div>
              <a :href="`/product/${product.slug}`" class="btn btn-primary btn-sm rounded-lg px-6 no-animation">
                详情
              </a>
            </div>
          </div>
        </article>
      </div>

      <div v-else class="flex flex-col items-center justify-center py-20 bg-white rounded-2xl border border-dashed border-base-300 text-base-content/40">
        <p class="text-lg font-medium">暂无在售商品</p>
      </div>
    </section>
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
  if (activeCategoryId.value === null) {
    return catalog.products;
  }
  return catalog.products.filter((product) => product.categoryId === activeCategoryId.value);
});
</script>

<style scoped>
.line-clamp-1 {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 1;
  overflow: hidden;
}
</style>
