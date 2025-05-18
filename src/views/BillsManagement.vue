<script setup lang="ts">
import BaseDeleteDialog from '@/components/base/BaseDeleteDialog.vue'
import BaseDrawer from '@/components/base/BaseDrawer.vue'
import BasePagination from '@/components/base/BasePagination.vue'
import DropdownButton from '@/components/base/DropdownButton.vue'
import BillsFilter from '@/components/billsManagement/BillsFilter.vue'
import BillsForm from '@/components/billsManagement/BillsForm.vue'
import BillsTable from '@/components/billsManagement/BillsTable.vue'
import type { BillsFilterType } from '@/models/bills'
import type { Pagination } from '@/models/share'
import BaseDialog from '@/components/base/BaseDialog.vue'
import BaseConfirmDialog from '@/components/base/BaseConfirmDialog.vue'
import BillsHeader from '@/components/billsManagement/BillsHeader.vue'
import BillsView from '@/components/billsManagement/BillsView.vue'

const items = ref([])
const statistics = reactive({
  totalPaidAmount: 0,
  totalUnpaidAmount: 0,
  executed: 0,
  pending: 0,
})
const itemIdToDelete = ref<string | null>(null)
const itemIdToEdit = ref<string | null>(null)
const itemIdToView = ref<string | null>(null)
const showDeleteDialog = ref(false)
const showFormDrawer = ref(false)
const showViewDrawer = ref(false)
const loading = ref(false)

const pagination = ref<Pagination>({
  currentPage: 1,
  totalItems: 0,
  itemsPerPage: 15,
  maxVisiblePages: 4,
})

const filter = ref<BillsFilterType>({
  billNumber: '',
  paidStatus: '',
  billStatus: '',
  receivingStation: '',
  issuingDateStart: '',
  issuingDateEnd: '',
  executionDateStart: '',
  executionDateEnd: '',
})

function handleDelete(id: string | undefined | null) {
  if (!id) return

  itemIdToDelete.value = id
  showDeleteDialog.value = true
}

function handleEdit(id: string | undefined | null) {
  if (!id) return

  itemIdToEdit.value = id
  showViewDrawer.value = false
  showFormDrawer.value = true
}

function handleView(id: string | undefined | null) {
  if (!id) return

  itemIdToView.value = id
  showViewDrawer.value = true
}

function handleFormDone() {
  showFormDrawer.value = false
  getBills()
}

async function getBills() {
  loading.value = true
  baseService
    .get('api/bills', {
      page: pagination.value.currentPage,
      perPage: pagination.value.itemsPerPage,
      ...filter.value,
    })
    .then((res: any) => {
      pagination.value.totalItems = res.totalCount
      statistics.totalPaidAmount = res.totalPaidAmount
      statistics.totalUnpaidAmount = res.totalUnpaidAmount
      statistics.executed = res.executedCount
      statistics.pending = res.pendingCount
      items.value = res.items
    })
    .finally(() => {
      loading.value = false
    })
}

function handleAdd() {
  itemIdToEdit.value = null
  showFormDrawer.value = true
}

function handleFilter() {
  if (pagination.value.currentPage === 1) {
    getBills()
  } else {
    pagination.value.currentPage = 1
  }
}

function resetFilter() {
  Object.assign(filter.value, {
    billNumber: '',
    paidStatus: '',
    billStatus: '',
    receivingStation: '',
    issuingDateStart: '',
    issuingDateEnd: '',
    executionDateStart: '',
    executionDateEnd: '',
  })
  if (pagination.value.currentPage === 1) {
    getBills()
  } else {
    pagination.value.currentPage = 1
  }
}

onMounted(() => {
  getBills()
})

watch(
  () => filter.value,
  () => {
    if (pagination.value.currentPage === 1) {
      getBills()
    } else {
      pagination.value.currentPage = 1
    }
  },
  {
    deep: true,
  },
)

watch(
  () => pagination.value.currentPage,
  (v) => {
    getBills()
  },
)

watch(
  () => showFormDrawer.value,
  (v) => {
    if (!v) {
      itemIdToEdit.value = null
    }
  },
)
</script>

<template>
  <div
    class="flex flex-col h-full gap-4 p-[2px] relative bg-[#2c2d2e] rounded-lg bg-[linear-gradient(143deg,#0075ff_0px,#2c2d2e_55px)]"
  >
    <div class="flex flex-col h-full gap-4 rounded-md bg-[#090b0d] relative z-20">
      <BillsHeader :total-items="pagination.totalItems" :statistics="statistics" @add="handleAdd" />
      <div class="p-2 flex flex-col gap-2 h-full">
        <div class="bg-[#131415] rounded-lg shadow-md overflow-hidden">
          <BaseExpandPanel title="Filters">
            <BillsFilter v-model="filter" @filter="handleFilter" />
            <div class="flex justify-end mt-4 gap-2">
              <button
                @click="resetFilter"
                class="px-4 py-2 text-gray-400 bg-[#27292b] rounded-md hover:bg-[#323436] transition-colors"
              >
                Reset
              </button>
              <button
                @click="getBills"
                class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 transition-colors"
              >
                Apply Filter
              </button>
            </div>
          </BaseExpandPanel>
        </div>

        <BillsTable
          :loading="loading"
          :items="items"
          @delete="handleDelete"
          @edit="handleEdit"
          @view="handleView"
        />

        <BasePagination
          v-model:currentPage="pagination.currentPage"
          :totalItems="pagination.totalItems"
          :itemsPerPage="pagination.itemsPerPage"
          :maxVisiblePages="pagination.maxVisiblePages"
        />

        <BaseDeleteDialog
          @done="getBills"
          v-model="showDeleteDialog"
          :url="`/api/bills/${itemIdToDelete}`"
        />

        <BaseDialog v-model="showFormDrawer" :title="itemIdToEdit ? 'Edit Bill' : 'Add Bill'">
          <BillsForm :id="itemIdToEdit" @done="handleFormDone" />
        </BaseDialog>

        <BaseDialog v-model="showViewDrawer">
          <BillsView :id="itemIdToView" @edit="handleEdit" />
        </BaseDialog>
      </div>
    </div>
  </div>
</template>
