<script setup lang="ts">
interface Props {
  label: string
  items: { label: string; value: string }[] | []
}

const model = defineModel()
const props = withDefaults(defineProps<Props>(), {
  items: () => [],
})

const isOpen = ref(false)
const dropdownRef = ref(null)
const Showlabel = computed(
  () => props.items.find((item) => item.value === model.value)?.label || props.label,
)

const handleClickOutside = (event) => {
  if (dropdownRef.value && !dropdownRef.value.contains(event.target)) {
    isOpen.value = false
  }
}

const handleSelect = (value) => {
  model.value = value
  isOpen.value = false
}

onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})
</script>

<template>
  <div ref="dropdownRef" class="relative inline-block text-left w-full">
    <button
      @click="isOpen = !isOpen"
      type="button"
      class="inline-flex justify-between w-full px-4 py-2 text-[#e5e5e5] bg-[#1c1d1f] border border-[#2c2d2e] rounded-lg hover:bg-[#27292b] focus:outline-none focus:ring-2 focus:ring-indigo-500"
    >
      <span>{{ Showlabel }}</span>
      <div>
        <svg
          class="w-4 h-4 ml-2"
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 10 6"
        >
          <path
            stroke="currentColor"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="m1 1 4 4 4-4"
          />
        </svg>
      </div>
    </button>

    <div
      v-if="isOpen"
      class="absolute z-10 mt-2 bg-[#1c1d1f] border border-[#2c2d2e] divide-y divide-[#2c2d2e] rounded-lg shadow-lg w-full"
    >
      <ul class="py-2 text-sm text-[#e5e5e5]">
        <li v-for="item in items" :key="item.value">
          <button
            @click="handleSelect(item.value)"
            class="w-full text-left px-4 py-2 hover:bg-[#27292b] transition-colors"
          >
            {{ item.label }}
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>
