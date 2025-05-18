<script lang="ts" setup>
import type { Statistics } from '@/models/bills'
import { AuthStorageUtils } from '@/utils/auth/auth-storage-utils'
import { useRouter } from 'vue-router'

interface Props {
  totalItems: string | number
  statistics: Statistics
}

const emit = defineEmits(['add'])
const props = defineProps<Props>()
const { totalItems, statistics } = toRefs(props)
const router = useRouter()
const confirmDialog = ref(false)

function logout() {
  confirmDialog.value = false
  AuthStorageUtils.logout()
}
</script>

<template>
  <div class="flex flex-col items-start p-4 gap-4 overflow-auto">
    <div class="flex items-center gap-4 justify-between w-full">
      <h2 class="min-w-fit text-2xl font-bold text-[#e5e5e5] flex-shrink">Bills Management</h2>
      <div class="flex gap-2 flex-shrink-0">
        <button
          @click="emit('add')"
          class="flex min-w-fit items center gap-[3px] px-3 h-[34px] justify-center items-center  text-[#e5e5e5] bg-[#ffffff26] rounded-lg shadow-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 "
        >
          <svg
            class="w-4 h-4"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M12 6v6m0 0v6m0-6h6m-6 0H6"
            ></path>
          </svg>
          <span class="hidden md:block">Add Bill</span>
        </button>
        <button
          @click="confirmDialog = true"
          class="flex min-w-fit items center gap-2 px-2 justify-center  items-center h-[34px] text-[#e5e5e5] bg-[#ffffff26] rounded-lg shadow-md hover:bg-red-600 focus:outline-none focus:ring-2 focus:ring-red-500 "
        >
          <svg
            class="w-6 h-6"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"
            ></path>
          </svg>
        </button>
      </div>
    </div>
    <div class="flex-1 flex gap-4 flex-wrap">
      <div class="flex md:items-center border border-[#797c7f] rounded-lg text-[#e5e5e5] min-w-fit gap-1 break-words bg-[#ffffff1a] px-[6px] text-[12px]">
        <div class="size-1 bg-[#797c7f] rounded-full"></div>
        <span>Paid Amount:</span>
        <b>{{ Number(statistics.totalPaidAmount).toLocaleString() }}</b>
      </div>
      <div class="flex md:items-center text-[#e5e5e5] rounded-lg px-[6px] text-[12px] border border-[#88071d] bg-[#00151f] min-w-fit gap-1 break-words">
        <div class="size-1 bg-[#88071d] rounded-full"></div>
        <span>Unpaid Amount:</span>
        <b>{{ Number(statistics.totalUnpaidAmount).toLocaleString() }}</b>
      </div>
      <div class="flex border border-[#00679a] rounded-lg text-[#e5e5e5] px-[6px] text-[12px] bg-[#00679a33] md:items-center min-w-fit gap-1 break-words">
        <div class="size-1 bg-[#00679a] rounded-full"></div>
        <span>Total Bills:</span>
        <b>{{ totalItems }}</b>
      </div>
      <div class="flex md:items-center min-w-fit gap-1 break-words text-[#e5e5e5] rounded-lg px-[6px] text-[12px] border border-[#db4726] bg-[#db472633]">
        <div class="size-1 bg-[#db4726] rounded-full"></div>
        <span>Executed:</span>
        <b>{{ statistics.executed }}</b>
      </div>
      <div class="flex md:items-center min-w-fit gap-1 break-words text-[#e5e5e5] rounded-lg px-[6px] text-[12px] border border-[#ffb700] bg-[#ffb70033]">
        <div class="size-1 bg-[#ffb700] rounded-full"></div>
        <span>Pending:</span>
        <b>{{ statistics.pending }}</b>
      </div>
    </div>
    <BaseConfirmDialog
      v-model="confirmDialog"
      title="Logout"
      message="Are you sure you want to logout?"
      @confirm="(v) => v && logout()"
    />
  </div>
</template>
