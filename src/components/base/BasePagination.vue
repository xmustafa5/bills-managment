<script lang="ts" setup>
import type { Pagination } from '@/models/share'
import { computed } from 'vue'

const props = withDefaults(defineProps<Pagination>(), {
  maxVisiblePages: 5,
})

const emit = defineEmits<{
  'update:currentPage': [page: number]
}>()

const totalPages = computed(() => Math.ceil(props.totalItems / props.itemsPerPage))
const startItem = computed(() => (props.currentPage - 1) * props.itemsPerPage + 1)
const endItem = computed(() => Math.min(startItem.value + props.itemsPerPage - 1, props.totalItems))

const pages = computed(() => {
  const pages: (number | string)[] = []
  const halfVisible = Math.floor(props.maxVisiblePages / 2)
  let start = Math.max(props.currentPage - halfVisible, 1)
  let end = Math.min(start + props.maxVisiblePages - 1, totalPages.value)

  if (end - start + 1 < props.maxVisiblePages) {
    start = Math.max(end - props.maxVisiblePages + 1, 1)
  }

  if (start > 1) {
    pages.push(1)
    if (start > 2) pages.push('...')
  }

  for (let i = start; i <= end; i++) {
    pages.push(i)
  }

  if (end < totalPages.value) {
    if (end < totalPages.value - 1) pages.push('...')
    pages.push(totalPages.value)
  }

  return pages
})

const handlePageChange = (page: number) => {
  if (page >= 1 && page <= totalPages.value) {
    emit('update:currentPage', page)
  }
}
</script>

<template>
  <nav
    class="flex flex-col md:flex-row justify-between items-start md:items-center p-4 border-t border-[#434446]"
    aria-label="Table navigation"
  >
    <span class="w-full md:text-start text-center text-sm font-normal text-[#e5e5e5]">
      Showing
      <span class="font-semibold text-[#e5e5e5]">{{ startItem }}-{{ endItem }}</span>
      of
      <span class="font-semibold text-[#e5e5e5]">{{ totalItems }}</span>
    </span>
    <ul class="inline-flex items-stretch">
      <li>
        <button
          @click="handlePageChange(currentPage - 1)"
          :disabled="currentPage === 1"
          class="flex items-center justify-center h-full p-2 text-[#e5e5e5] bg-transparent border border-[#434446] hover:bg-[#ffffff08] transition-colors disabled:opacity-50 rounded-l-md"
        >
          <span class="sr-only">Previous</span>
          <svg
            class="w-5 h-5"
            aria-hidden="true"
            fill="currentColor"
            viewbox="0 0 20 20"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              fill-rule="evenodd"
              d="M12.707 5.293a1 1 0 010 1.414L9.414 10l3.293 3.293a1 1 0 01-1.414 1.414l-4-4a1 1 0 010-1.414l4-4a1 1 0 011.414 0z"
              clip-rule="evenodd"
            />
          </svg>
        </button>
      </li>
      <li v-for="page in pages" :key="page">
        <button
          @click="typeof page === 'number' ? handlePageChange(page) : null"
          :class="[
            'flex items-center justify-center text-sm py-2 px-3 border border-[#434446] transition-colors',
            currentPage === page
              ? 'text-white bg-[#ffffff15]'
              : 'text-[#e5e5e5] bg-transparent hover:bg-[#ffffff08]',
            typeof page !== 'number' ? 'cursor-default' : '',
          ]"
        >
          {{ page }}
        </button>
      </li>
      <li>
        <button
          @click="handlePageChange(currentPage + 1)"
          :disabled="currentPage === totalPages"
          class="flex items-center justify-center h-full p-2 text-[#e5e5e5] bg-transparent border border-[#434446] hover:bg-[#ffffff08] transition-colors disabled:opacity-50 rounded-r-md"
        >
          <span class="sr-only">Next</span>
          <svg
            class="w-5 h-5"
            aria-hidden="true"
            fill="currentColor"
            viewbox="0 0 20 20"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              fill-rule="evenodd"
              d="M7.293 14.707a1 1 0 010-1.414L10.586 10 7.293 6.707a1 1 0 011.414-1.414l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414 0z"
              clip-rule="evenodd"
            />
          </svg>
        </button>
      </li>
    </ul>
  </nav>
</template>
