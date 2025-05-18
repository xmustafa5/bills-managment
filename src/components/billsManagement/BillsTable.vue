<script lang="ts" setup>
import type { Bills } from '@/models/bills'

interface Props {
  items: Bills[] | []
  loading: boolean
}
interface Emit {
  (event: 'edit', id: string | undefined): void
  (event: 'delete', id: string | undefined): void
  (event: 'view', id: string | undefined): void
}

const emit = defineEmits<Emit>()
const props = withDefaults(defineProps<Props>(), {
  items: () => [],
})
</script>

<template>
  <div class="flex-1 w-full overflow-auto bg-[#131415] rounded-lg shadow-md">
    <table
      class="overflow-hidden mt-2 [border-collapse:separate] border-spacing-0 w-full rounded-[10px] border-[1px] border-[#2c2d2e]"
    >
      <thead
        class="text-xs uppercase shadow-[0px_4px_8px_0px_rgba(0,0,0,0.15)] sticky top-0 bg-[#131415]"
      >
        <tr class="[&_button]:hover:opacity-100 hover:bg-[#ffffff08] transition-colors">
          <th scope="col" class="relative py-[2px] px-[2px] border-b-[2px] border-[#434446]">
            <div
              class="text-[#fff] w-full flex items-center px-[4px] text-start bg-transparent hover:bg-background-presentation-action-hover transition-[background-color,color] duration-200 rounded-[3px] h-[32px] typography-body-medium-semibold min-w-[10px] relative"
            >
              Bill Number
              <div class="h-[80%] bg-[#2c2d2e] w-[1px] absolute -right-[2px]"></div>
            </div>
          </th>
          <th scope="col" class="relative py-[2px] px-[2px] border-b-[2px] border-[#434446]">
            <div
              class="text-[#fff] w-full flex items-center px-[4px] text-start bg-transparent hover:bg-background-presentation-action-hover transition-[background-color,color] duration-200 rounded-[3px] h-[32px] typography-body-medium-semibold min-w-[10px] relative"
            >
              Receiver
              <div class="h-[80%] bg-[#2c2d2e] w-[1px] absolute -right-[2px]"></div>
            </div>
          </th>
          <th scope="col" class="relative py-[2px] px-[2px] border-b-[2px] border-[#434446]">
            <div
              class="text-[#fff] w-full flex items-center px-[4px] text-start bg-transparent hover:bg-background-presentation-action-hover transition-[background-color,color] duration-200 rounded-[3px] h-[32px] typography-body-medium-semibold min-w-[10px] relative"
            >
              Amount
              <div class="h-[80%] bg-[#2c2d2e] w-[1px] absolute -right-[2px]"></div>
            </div>
          </th>
          <th scope="col" class="relative py-[2px] px-[2px] border-b-[2px] border-[#434446]">
            <div
              class="text-[#fff] w-full flex items-center px-[4px] text-start bg-transparent hover:bg-background-presentation-action-hover transition-[background-color,color] duration-200 rounded-[3px] h-[32px] typography-body-medium-semibold min-w-[10px] relative"
            >
              Paid Status
              <div class="h-[80%] bg-[#2c2d2e] w-[1px] absolute -right-[2px]"></div>
            </div>
          </th>
          <th scope="col" class="relative py-[2px] px-[2px] border-b-[2px] border-[#434446]">
            <div
              class="text-[#fff] w-full flex items-center px-[4px] text-start bg-transparent hover:bg-background-presentation-action-hover transition-[background-color,color] duration-200 rounded-[3px] h-[32px] typography-body-medium-semibold min-w-[10px] relative"
            >
              Issuing Date
              <div class="h-[80%] bg-[#2c2d2e] w-[1px] absolute -right-[2px]"></div>
            </div>
          </th>
          <th scope="col" class="relative py-[2px] px-[2px] border-b-[2px] border-[#434446]">
            <div
              class="text-[#fff] w-full flex items-center px-[4px] text-start bg-transparent hover:bg-background-presentation-action-hover transition-[background-color,color] duration-200 rounded-[3px] h-[32px] typography-body-medium-semibold min-w-[10px] relative"
            >
              Execution Date
              <div class="h-[80%] bg-[#2c2d2e] w-[1px] absolute -right-[2px]"></div>
            </div>
          </th>
          <th scope="col" class="relative py-[2px] px-[2px] border-b-[2px] border-[#434446]">
            <div
              class="text-[#fff] w-full flex items-center px-[4px] text-start bg-transparent hover:bg-background-presentation-action-hover transition-[background-color,color] duration-200 rounded-[3px] h-[32px] typography-body-medium-semibold min-w-[10px] relative"
            >
              Receiving Station
              <div class="h-[80%] bg-[#2c2d2e] w-[1px] absolute -right-[2px]"></div>
            </div>
          </th>
          <th scope="col" class="relative py-[2px] px-[2px] border-b-[2px] border-[#434446]">
            <div
              class="text-[#fff] w-full flex items-center px-[4px] text-start bg-transparent hover:bg-background-presentation-action-hover transition-[background-color,color] duration-200 rounded-[3px] h-[32px] typography-body-medium-semibold min-w-[10px]"
            >
              Actions
            </div>
          </th>
        </tr>
      </thead>
      <tbody>
        <template v-if="loading">
          <RowTableSkeleton />
        </template>
        <template v-else-if="items.length === 0">
          <tr>
            <td colspan="8" class="py-8 text-center text-[#e5e5e5]">
              No bills found. Try adjusting your filters or add a new bill.
            </td>
          </tr>
        </template>
        <tr
          v-for="item in items"
          v-if="!loading"
          :key="item.id"
          class="hover:bg-[#ffffff08] transition-colors"
        >
          <td
            class="truncate h-[40px] text-[#e5e5e5] relative border-r border-b border-[#434446] px-1 rtl:border-l rtl:border-r-0 break-all text-[12px]"
          >
            {{ item.billNumber }}
          </td>
          <td
            class="truncate h-[40px] text-[#e5e5e5] relative border-r border-b border-[#434446] px-1 rtl:border-l rtl:border-r-0 break-all text-[12px] font-normal"
          >
            {{ item.receiver }}
          </td>
          <td
            class="truncate h-[40px] text-[#e5e5e5] relative border-r border-b border-[#434446] px-1 rtl:border-l rtl:border-r-0 break-all text-[12px] font-normal"
          >
            {{ Number(item.amount).toLocaleString() }}
          </td>
          <td
            class="truncate h-[40px] text-[#e5e5e5] relative border-r border-b border-[#434446] px-1 rtl:border-l rtl:border-r-0 break-all text-[12px] font-normal"
          >
            <div
              :class="[
                'flex items-center gap-1 px-[6px] py-1 rounded-lg w-fit',
                item.paidStatus === 'paid'
                  ? 'border border-[#00679a] bg-[#00679a33]'
                  : 'border border-[#88071d] bg-[#00151f]',
              ]"
            >
              <div
                :class="[
                  'size-1 rounded-full',
                  item.paidStatus === 'paid' ? 'bg-[#00679a]' : 'bg-[#88071d]',
                ]"
              ></div>
              {{ item.paidStatus }}
            </div>
          </td>
          <td
            class="truncate h-[40px] text-[#e5e5e5] relative border-r border-b border-[#434446] px-1 rtl:border-l rtl:border-r-0 break-all text-[12px] font-normal"
          >
            {{ item.issuingDate }}
          </td>
          <td
            class="truncate h-[40px] text-[#e5e5e5] relative border-r border-b border-[#434446] px-1 rtl:border-l rtl:border-r-0 break-all text-[12px] font-normal"
          >
            {{ item.executionDate }}
          </td>
          <td
            class="truncate h-[40px] text-[#e5e5e5] relative border-r border-b border-[#434446] px-1 rtl:border-l rtl:border-r-0 break-all text-[12px] font-normal"
          >
            {{ item.receivingStation }}
          </td>
          <td
            class="flex items-center gap-4 truncate h-[43px] text-[#e5e5e5] relative border-r border-b border-[#434446] px-1 rtl:border-l rtl:border-r-0 break-all text-[12px] font-normal"
          >
            <span
              class="cursor-pointer hover:bg-[#ffffff15] p-1 rounded-md transition-colors duration-200"
              @click.stop="emit('view', item.id)"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                stroke="currentColor"
                class="size-6"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M2.036 12.322a1.012 1.012 0 0 1 0-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178Z"
                />
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z"
                />
              </svg>
            </span>
            <span
              class="cursor-pointer hover:bg-[#ffffff15] p-1 rounded-md transition-colors duration-200"
              @click.stop="emit('edit', item.id)"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                stroke="currentColor"
                class="size-6"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="m16.862 4.487 1.687-1.688a1.875 1.875 0 1 1 2.652 2.652L6.832 19.82a4.5 4.5 0 0 1-1.897 1.13l-2.685.8.8-2.685a4.5 4.5 0 0 1 1.13-1.897L16.863 4.487Zm0 0L19.5 7.125"
                />
              </svg>
            </span>
            <span
              class="cursor-pointer hover:bg-[#ffffff15] p-1 rounded-md transition-colors duration-200"
              @click.stop="emit('delete', item.id)"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                stroke="currentColor"
                class="size-6"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0"
                />
              </svg>
            </span>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
