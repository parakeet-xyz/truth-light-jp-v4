<script setup lang="ts">
import ContentSubstancesTable from '~/components/content/SubstancesTable.client.vue'

definePageMeta({ layout: "substances" })

useSeoMeta({
  title: `NPSデータベース(α版)`,
  description: `現在日本で規制されている薬物やNPS(新規精神活性物質)のデータベース`,
  ogDescription: `現在日本で規制されている薬物やNPS(新規精神活性物質)のデータベース`,
})

</script>

<template>
  <ClientOnly>
    <ContentSubstancesTable />
    <template #fallback>
      <div class="text-base text-center pt-12">Loading…</div>
    </template>
  </ClientOnly>
</template>
