<script lang="ts" setup>
import type { Bills } from '@/models/bills'

interface Props {
  id: string | null
}

interface Emit {
  (event: 'edit', id: string | null): void
}
const props = defineProps<Props>()
const emit = defineEmits<Emit>()
const isLoading = ref(false)
const item = reactive<Bills>({
  id: '',
  billNumber: '',
  receiver: '',
  amount: '',
  paidStatus: '',
  billStatus: '',
  issuingDate: '',
  executionDate: '',
  receivingStation: '',
  transactions: [],
})

const getBill = async () => {
  isLoading.value = true
  await baseService
    .get(`/api/bills/${props.id}`)
    .then((res: any) => {
      item.id = res.id
      item.billNumber = res.billNumber
      item.receiver = res.receiver
      item.amount = res.amount
      item.paidStatus = res.paidStatus
      item.billStatus = res.billStatus
      item.issuingDate = res.issuingDate
      item.executionDate = res.executionDate
      item.receivingStation = res.receivingStation
      item.transactions = res.transactions
    })
    .finally(() => {
      isLoading.value = false
    })
}

watch(
  () => props.id,
  () => {
    if (props.id) {
      getBill()
    }
  },
  {
    immediate: true,
    deep: true,
  },
)
</script>

<template>
  <div class="max-w-2xl mx-auto max-h-[80vh] bg-[#1c1d1f] rounded-lg shadow p-6 overflow-auto">
    <div class="flex justify-between items-center mb-6">
      <h2 class="text-xl font-bold text-[#e5e5e5]">
        <span
          v-if="isLoading"
          class="animate-pulse h-8 bg-[#37383a] rounded w-32 inline-block"
        ></span>
        <span v-else>Bill #{{ item.billNumber }}</span>
      </h2>
      <button
        @click="emit('edit', id)"
        class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 transition-colors"
      >
        Edit
      </button>
    </div>

    <div class="grid grid-cols-1 gap-6">
      <div class="bg-[#131415] rounded-lg p-4 border border-[#2c2d2e]">
        <h3 class="text-lg font-medium text-[#e5e5e5] mb-4">Bill Details</h3>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div class="space-y-3">
            <div class="flex justify-between">
              <span class="text-gray-400">Receiver:</span>
              <span v-if="isLoading" class="animate-pulse h-5 bg-[#37383a] rounded w-20"></span>
              <span v-else class="font-medium text-[#e5e5e5]">{{ item.receiver }}</span>
            </div>

            <div class="flex justify-between">
              <span class="text-gray-400">Amount:</span>
              <span v-if="isLoading" class="animate-pulse h-5 bg-[#37383a] rounded w-20"></span>
              <span v-else class="font-medium text-[#e5e5e5]">
                {{ Number(item.amount).toLocaleString() }}
              </span>
            </div>

            <div class="flex justify-between">
              <span class="text-gray-400">Receiving Station:</span>
              <span v-if="isLoading" class="animate-pulse h-5 bg-[#37383a] rounded w-20"></span>
              <span v-else class="font-medium text-[#e5e5e5]">{{ item.receivingStation }}</span>
            </div>
          </div>

          <div class="space-y-3">
            <div class="flex justify-between items-center">
              <span class="text-gray-400">Payment Status:</span>
              <span v-if="isLoading" class="animate-pulse h-5 bg-[#37383a] rounded w-20"></span>
              <span
                v-else
                class="flex border rounded-lg text-[#e5e5e5] px-[6px] text-[12px] items-center min-w-fit gap-1 break-words"
                :class="{
                  'border-[#089c54] bg-[#089c5433]': item.paidStatus === 'paid',
                  'border-[#88071d] bg-[#00151f]': item.paidStatus !== 'paid',
                }"
              >
                <div
                  class="size-1 rounded-full"
                  :class="{
                    'bg-[#089c54]': item.paidStatus === 'paid',
                    'bg-[#88071d]': item.paidStatus !== 'paid',
                  }"
                ></div>
                {{ item.paidStatus }}
              </span>
            </div>

            <div class="flex justify-between items-center">
              <span class="text-gray-400">Bill Status:</span>
              <span v-if="isLoading" class="animate-pulse h-5 bg-[#37383a] rounded w-20"></span>
              <span v-else class="font-medium text-[#e5e5e5]">{{ item.billStatus }}</span>
            </div>
          </div>
        </div>
      </div>

      <div class="bg-[#131415] rounded-lg p-4 border border-[#2c2d2e]">
        <h3 class="text-lg font-medium text-[#e5e5e5] mb-4">Transactions</h3>

        <div v-if="isLoading" class="space-y-3">
          <div
            v-for="i in 3"
            :key="i"
            class="animate-pulse flex justify-between items-center p-4 bg-[#1c1d1f] rounded-lg"
          >
            <div class="flex items-center space-x-3">
              <div class="flex-shrink-0 w-2 h-2 bg-[#37383a] rounded-full"></div>
              <div class="h-5 bg-[#37383a] rounded w-24"></div>
            </div>
            <div class="h-5 bg-[#37383a] rounded w-20"></div>
          </div>
        </div>

        <div
          v-else-if="!item.transactions || item.transactions.length === 0"
          class="flex flex-col items-center justify-center py-8 text-gray-500"
        >
          <svg
            class="w-12 h-12 mb-3 text-gray-400"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="1.5"
              d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"
            />
          </svg>
          <p class="text-sm">No transactions recorded yet</p>
        </div>

        <div v-else class="space-y-2">
          <div
            v-for="transaction in item.transactions"
            :key="transaction.paymentDate"
            class="flex justify-between items-center p-4 bg-[#27292b] rounded-lg transition-colors duration-200 hover:bg-[#323436]"
          >
            <div class="flex items-center space-x-3">
              <div class="flex-shrink-0 w-2 h-2 bg-green-500 rounded-full"></div>
              <span class="font-medium text-[#e5e5e5]">
                {{ Number(transaction.amount).toLocaleString() }}
              </span>
            </div>
            <div class="text-sm text-[#e5e5e5]">
              {{
                new Date(transaction.paymentDate).toLocaleDateString('en-US', {
                  year: 'numeric',
                  month: 'short',
                  day: 'numeric',
                })
              }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
