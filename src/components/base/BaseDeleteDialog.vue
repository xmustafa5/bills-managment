<script lang="ts" setup>
interface Props {
  url: string
  title?: string
  message?: string
}
const props = defineProps<Props>()
const model = defineModel()
const emit = defineEmits(['done'])
const isLoading = ref(false)

async function handleConfirm() {
  isLoading.value = true
  if (!props.url) return
  baseService
    .delete(props.url)
    .then(() => {
      model.value = false
      emit('done')
    })
    .finally(() => {
      isLoading.value = false
    })
}
</script>

<template>
  <Transition name="modal">
    <div
      v-if="model"
      class="relative z-10"
      aria-labelledby="modal-title"
      role="dialog"
      aria-modal="true"
    >
      <div class="fixed inset-0 bg-[#202020c2] transition-opacity" aria-hidden="true"></div>

      <div class="fixed inset-0 z-10 w-screen overflow-y-auto">
        <div
          class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0"
        >
          <div
            class="relative transform overflow-hidden rounded-lg bg-[#131415] text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-lg"
          >
            <div class="bg-[#131415] px-4 pb-4 pt-5 sm:p-6 sm:pb-4">
              <div class="sm:flex sm:items-start">
                <div
                  class="mx-auto flex size-12 shrink-0 items-center justify-center rounded-full bg-[#88071d] sm:mx-0 sm:size-10"
                >
                  <svg
                    class="size-6 text-[#ff516e]"
                    fill="none"
                    viewBox="0 0 24 24"
                    stroke-width="1.5"
                    stroke="currentColor"
                    aria-hidden="true"
                    data-slot="icon"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      d="M12 9v3.75m-9.303 3.376c-.866 1.5.217 3.374 1.948 3.374h14.71c1.73 0 2.813-1.874 1.948-3.374L13.949 3.378c-.866-1.5-3.032-1.5-3.898 0L2.697 16.126ZM12 15.75h.007v.008H12v-.008Z"
                    />
                  </svg>
                </div>
                <div class="mt-3 text-center sm:ml-4 sm:mt-0 sm:text-left">
                  <h3 class="text-base font-semibold text-[#e5e5e5]" id="modal-title">
                    {{ props.title || 'Delete' }}
                  </h3>
                  <div class="mt-2">
                    <p class="text-sm text-[#e5e5e5]">
                      {{ props.message || 'Are you sure you want to delete this item?' }}
                    </p>
                  </div>
                </div>
              </div>
            </div>
            <div class="bg-[#2c2d2e] px-4 py-3 sm:flex sm:flex-row-reverse sm:px-6">
              <button
                @click="handleConfirm"
                type="button"
                class="w-28 inline-flex justify-center rounded-md bg-red-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-red-500 sm:ml-3"
              >
                <BaseLoadingSpinner v-if="isLoading" />
                <span v-else> Confirm </span>
              </button>
              <button
                @click="model = false"
                type="button"
                class="mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50 sm:mt-0 sm:w-auto"
              >
                Cancel
              </button>
            </div>
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

.modal-enter-active .modal,
.modal-leave-active .modal {
  transition: transform 0.3s ease;
}

.modal-enter-from .modal-right,
.modal-leave-to .modal-right {
  transform: translateX(100%);
}

.modal-enter-from .modal-left,
.modal-leave-to .modal-left {
  transform: translateX(-100%);
}
</style>
