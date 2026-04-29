<template>
  <div class="max-w-7xl mx-auto px-4 py-8 space-y-12 bg-base-50">
    
    <header class="relative rounded-2xl bg-white border border-base-200 p-8 md:p-12 overflow-hidden shadow-sm">
      <div class="relative z-10 max-w-2xl">
        <h1 class="text-3xl md:text-4xl font-extrabold text-base-content tracking-tight">
          {{ site.siteSubtitle || '官方精品商城' }}
        </h1>
        <p v-if="site.notice" class="mt-4 text-base-content/60 text-lg leading-relaxed">
          {{ site.notice }}
        </p>
        <div class="mt-8 flex gap-4">
          <div class="flex flex-col">
            <span class="text-sm text-base-content/40 uppercase font-bold tracking-wider">在售商品</span>
            <span class="text-2xl font-mono font-bold text-primary">{{ catalog.products.length }} 款</span>
          </div>
          <div class="divider divider-horizontal"></div>
          <div class="flex flex-col">
            <span class="text-sm text-base-content/40 uppercase font-bold tracking-wider">服务保证</span>
            <span class="text-2xl font-mono font-bold text-success">自动发货</span>
          </div>
        </div>
      </div>
      <div class="absolute top-0 right-0 -translate-y-1/2 translate-x-1/2 w-96 h-96 bg-primary/5 rounded-full blur-3xl"></div>
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
              class="h-full w-full object-cover transition-transform duration-500 group-hover:scale-110"
              :alt="product.name"
            />
          </a>

          <div class="flex flex-col p-5 flex-grow">
            <div class="flex justify-between items-start mb-2">
              <span class="text-[10px] px-2 py-0.5 rounded bg-base-100 text-base-content/50 font-bold uppercase">
                {{ product.categoryName || '未分类' }}
              </span>
              <div class="badge badge-outline badge-xs opacity-50 font-mono italic">#{{ product.slug }}</div>
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
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1" stroke="currentColor" class="w-16 h-16 mb-4 opacity-20">
          <path stroke-linecap="round" stroke-linejoin="round" d="m20.25 7.5-.625 10.632a2.25 2.25 0 0 1-2.247 2.118H6.622a2.25 2.25 0 0 1-2.247-2.118L3.75 7.5M10 11.25h4M3.375 7.5h17.25c.621 0 1.125-.504 1.125-1.125v-1.5c0-.621-.504-1.125-1.125-1.125H3.375c-.621 0-1.125.504-1.125 1.125v1.5c0 .621.504 1.125 1.125 1.125Z" />
        </svg>
        <p class="text-lg font-medium">暂无在售商品</p>
        <p class="text-sm">请联系管理员上架商品</p>
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
/* 移除之前复杂的 hero-img 和 border 样式 */
.line-clamp-1 {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 1;
  overflow: hidden;
}
</style>
