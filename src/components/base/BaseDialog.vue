<script lang="ts" setup>
const model = defineModel()
defineProps({
  title: {
    type: String,
    default: '',
  },
})
</script>

<template>
  <Transition name="modal">
    <div
      v-if="model"
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50"
    >
      <div
        class="flex flex-col w-fit justify-center items-center p-[2px] relative bg-[#2c2d2e] rounded-lg bg-[linear-gradient(143deg,#0075ff_0px,#2c2d2e_55px)]"
      >
        <div class="bg-[#131415] max-w-2xl w-full mx-4 shadow-xl rounded-t-[6px] overflow-hidden">
          <div
            v-if="$slots.title || title"
            class="border-b relative bg-[#000] border-[#2c2d2e] px-6 py-4"
          >
            <div
              class="absolute top-[0px] w-[170px] rounded-t-[6px] z-10 h-full left-[0px] bg-[linear-gradient(132deg,#0d0f4e_0%,#000_50%)]"
            ></div>
            <h3 class="text-xl font-semibold text-[#e5e5e5] relative z-20">
              <slot name="title">{{ title }}</slot>
            </h3>
          </div>
          <button
            @click="model = false"
            class="absolute -top-3 -right-3 bg-red-500 text-white rounded-full p-2 hover:bg-red-600 transition-colors focus:outline-none focus:ring-2 focus:ring-red-700"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="h-4 w-4"
              viewBox="0 0 20 20"
              fill="currentColor"
            >
              <path
                fill-rule="evenodd"
                d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z"
                clip-rule="evenodd"
              />
            </svg>
          </button>
          <div class="p-1">
            <slot></slot>
          </div>
        </div>
      </div>
    </div>
  </Transition>
</template>

<style scoped>
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-active .bg-[#131415],
.modal-leave-active .bg-[#131415] {
  transition: transform 0.3s ease;
}

.modal-enter-from .bg-[#131415] {
  transform: scale(0.9);
}

.modal-leave-to .bg-[#131415] {
  transform: scale(0.9);
}
</style>
