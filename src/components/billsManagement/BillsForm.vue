<script setup lang="ts">
import { ref } from 'vue'
import { Form, useForm } from 'vee-validate'
import * as yup from 'yup'
import { Field } from 'vee-validate'
import type { Bills, Transaction } from '@/models/bills'

interface Props {
  id?: string | null
}

const props = defineProps<Props>()
const emit = defineEmits(['done'])

const isLoading = ref(false)
const getItemLoading = ref(false)

const billSchema = yup.object({
  billNumber: yup.string().required('Bill number is required'),
  receiver: yup.string().required('Receiver is required'),
  amount: yup.number().typeError('Amount must be a valid number').required('Amount is required'),
  paidStatus: yup.string().required('Paid status is required'),
  billStatus: yup.string().required('Bill status is required'),
  issuingDate: yup.string().required('Issuing date is required'),
  executionDate: yup.string().required('Execution date is required'),
  receivingStation: yup.string().required('Receiving station is required'),
})

const transactionSchema = yup.object({
  transactionAmount: yup
    .number()
    .typeError('Amount must be a valid number')
    .required('Amount is required'),
  transactionPaymentDate: yup.string().required('Payment date is required'),
})

const form = ref<Bills>({
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

const transactionForm = ref<Transaction>({
  amount: '',
  paymentDate: '',
})

const handleEditItem = (res: Bills) => {
  if (!res) return
  form.value.billNumber = res.billNumber
  form.value.receiver = res.receiver
  form.value.amount = res.amount
  form.value.paidStatus = res.paidStatus
  form.value.billStatus = res.billStatus
  form.value.issuingDate = res.issuingDate
  form.value.executionDate = res.executionDate
  form.value.receivingStation = res.receivingStation
  form.value.transactions = res.transactions
}

const getBill = async () => {
  getItemLoading.value = true
  await baseService
    .get(`/api/bills/${props.id}`)
    .then((res: object | null) => {
      if (res) handleEditItem(res as Bills)
    })
    .finally(() => {
      getItemLoading.value = false
    })
}

const handleAddTransaction = () => {
  form.value.transactions.push(transactionForm.value)
  transactionForm.value = {
    amount: '',
    paymentDate: '',
  }
}

const handelDeleteTransaction = (index: number) => {
  form.value.transactions.splice(index, 1)
}

const handleSub = async () => {
  const endpoint = props.id ? `/api/bills/${props.id}` : 'api/bills'
  isLoading.value = true
  await baseService[props.id ? 'update' : 'create'](endpoint, form.value)
    .then(() => {
      emit('done')
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
  <div class="mx-auto max-h-[600px] p-4 overflow-auto">
    <BaseFormSkeleton v-if="getItemLoading" />
    <Form
      v-else
      @submit="handleSub"
      :validation-schema="billSchema"
      class="space-y-6 h-full flex flex-col justify-between"
    >
      <div class="space-y-6">
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div class="col-span-2">
            <BaseTextField
              v-model="form.receiver"
              name="receiver"
              label="Receiver"
              type="text"
              placeholder="Receiver..."
              required
            />
          </div>
          <div class="form-group col-span-2">
            <BaseTextField
              v-model="form.receivingStation"
              name="receivingStation"
              label="Receiving Station"
              type="text"
              placeholder="Station..."
              required
            />
          </div>
          <div class="form-group col-span-2 md:col-span-1">
            <BaseTextField
              v-model="form.billNumber"
              name="billNumber"
              label="Bill Number"
              type="text"
              placeholder="BILL-001"
              required
            />
          </div>

          <div class="form-group col-span-2 md:col-span-1">
            <BaseTextField
              v-model="form.amount"
              name="amount"
              label="Amount"
              type="text"
              placeholder="1500"
              required
            />
          </div>
          <div class="form-group col-span-2 md:col-span-1">
            <BaseTextField
              v-model="form.paidStatus"
              name="paidStatus"
              label="Paid Status"
              type="select"
              required
              :options="[
                { value: 'paid', label: 'Paid' },
                { value: 'unpaid', label: 'Unpaid' },
              ]"
            />
          </div>
          <div class="form-group col-span-2 md:col-span-1">
            <BaseTextField
              v-model="form.billStatus"
              name="billStatus"
              label="Bill Status"
              type="select"
              required
              :options="[
                { value: 'pending', label: 'Pending' },
                { value: 'executed', label: 'Executed' },
              ]"
            />
          </div>
          <div class="form-group col-span-2 md:col-span-1">
            <BaseTextField
              v-model="form.issuingDate"
              name="issuingDate"
              label="Issuing Date"
              type="date"
              placeholder="2024-01-01"
              required
            />
          </div>
          <div class="form-group col-span-2 md:col-span-1">
            <BaseTextField
              v-model="form.executionDate"
              name="executionDate"
              label="Execution Date"
              type="date"
              placeholder="2024-01-15"
              required
            />
          </div>
        </div>

        <div class="border-t border-[#2c2d2e] pt-6">
          <h3 class="text-lg font-medium text-[#e5e5e5] mb-4">Transactions</h3>

          <div v-if="form.transactions.length > 0" class="mb-4 space-y-3">
            <div
              v-for="(transaction, index) in form.transactions"
              :key="index"
              class="flex justify-between items-center p-3 bg-[#131415] rounded-lg border border-[#2c2d2e] hover:border-[#434446] transition-colors"
            >
              <div class="flex flex-col">
                <span class="text-[#e5e5e5] font-medium">{{ Number(transaction.amount).toLocaleString() }}</span>
                <span class="text-sm text-gray-400">{{ transaction.paymentDate }}</span>
              </div>
              <button
                @click="handelDeleteTransaction(index)"
                class="text-red-500 hover:text-red-700 transition-colors"
              >
                ✕
              </button>
            </div>
          </div>

          <Form
            @submit="handleAddTransaction"
            :validation-schema="transactionSchema"
            class="bg-[#1c1d1f] p-4 rounded-lg border border-[#2c2d2e]"
          >
            <h4 class="text-md font-medium text-[#e5e5e5] mb-3">Add New Transaction</h4>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div class="form-group col-span-2 md:col-span-1">
                <BaseTextField
                  v-model="transactionForm.amount"
                  name="transactionAmount"
                  label="Amount"
                  type="text"
                  placeholder="1000"
                  required
                />
              </div>
              <div class="form-group col-span-2 md:col-span-1">
                <BaseTextField
                  v-model="transactionForm.paymentDate"
                  name="transactionPaymentDate"
                  label="Payment Date"
                  type="date"
                  placeholder="2024-01-01"
                  required
                />
              </div>
            </div>
            <div class="flex justify-end mt-4">
              <button
                type="submit"
                class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 transition-colors"
              >
                Add Transaction
              </button>
            </div>
          </Form>
        </div>
      </div>

      <div class="flex justify-end pt-4 border-t border-[#2c2d2e]">
        <button
          type="submit"
          class="px-6 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 transition-colors"
        >
          <BaseLoadingSpinner v-if="isLoading" />
          <span v-else> {{ props.id ? 'Update Bill' : 'Create Bill' }} </span>
        </button>
      </div>
    </Form>
  </div>
</template>
