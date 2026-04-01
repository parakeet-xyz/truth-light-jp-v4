<template>
  <main
    id="__nuxtContent"
  >
    <div class="grid place-items-center w-[100%] h-[250px] bg-[#25343F]">
      <div class="mx-auto max-w-6xl">
        <h1 class="custom-font-bold text-4xl tracking-wider text-[#EAEFEF]">
          {{ page?.title ?? '' }}
        </h1>
        <p></p>
      </div>
    </div>
    <div
      class="prose
      mx-auto max-w-6xl md:flex md:flex-row-reverse"
    >
      <!-- TOC -->
      <aside
        v-if="page?.body?.toc?.links?.length"
        class="w-full mx-6
        md:w-1/4"
        aria-label="Table of contents"
      >
        <nav class="rounded-lg border p-4">
          <p class="mb-3 text-sm font-semibold">目次</p>

          <ul class="space-y-2 text-sm">
            <li v-for="link in page.body.toc.links" :key="link.id">
              <a :href="`#${link.id}`" class="block hover:underline">
                {{ link.text }}
              </a>

              <ul
                v-if="link.children?.length"
                class="mt-2 ml-4 space-y-1"
              >
                <li v-for="child in link.children" :key="child.id">
                  <a :href="`#${child.id}`" class="block hover:underline">
                    {{ child.text }}
                  </a>
                </li>
              </ul>
            </li>
          </ul>
        </nav>
      </aside>

      <!-- 本文 -->
      <article
        class="flex-1"
      >
        <ContentRenderer v-if="page" :value="page" />  
      </article>
    </div>

  </main>
</template>

<script setup lang="ts">
import { withoutTrailingSlash } from 'ufo'

definePageMeta({ layout: 'default' })

const route = useRoute()
const path = computed(() => withoutTrailingSlash(route.path) || '/')

const { data: page } = await useAsyncData(
  () => `docs:${path.value}`,
  () => queryCollection('docs').path(path.value).first()
)

if (!page.value) {
  throw createError({ statusCode: 404, statusMessage: `Page not found: ${path.value}` })
}

// v3標準: seo は useSeoMeta と組み合わせる想定 :contentReference[oaicite:3]{index=3}
useSeoMeta({
  title: () => page.value?.seo?.title ?? page.value?.title,
  description: () => page.value?.seo?.description ?? page.value?.description,
  ogDescription: () => page.value?.seo?.description ?? page.value?.description,
})

// seo.meta / seo.link みたいな「配列系」は useHead 側に流す
useHead({
  meta: () => page.value?.seo?.meta ?? [],
  link: () => page.value?.seo?.link ?? [],
})
</script>