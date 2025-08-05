<template>
  <div class="flex p-2 pl-3 pb-1 m-3.5 border rounded tab-shadow w-fit">
    <div class="text-sm mr-1 select-none">
      <DropdownMenu>
        <DropdownMenuTrigger asChild>
          <span class="mx-1.5 cursor-pointer hover:text-gray-500">
            {{ startItem }}-{{ endItem - 1 }}
            <ChevronDownIcon class="w-4 h-4 inline" />
          </span>
        </DropdownMenuTrigger>
        <DropdownMenuContent class="w-fit">
          <DropdownMenuLabel>Page size</DropdownMenuLabel>
          <DropdownMenuSeparator />
          <DropdownMenuCheckboxItem
            v-for="page in pageSize"
            :key="page"
            @select="handlePageSizeChange(page)"
            :disabled="curPageSize === page"
          >
            {{ page }}
          </DropdownMenuCheckboxItem>
        </DropdownMenuContent>
      </DropdownMenu>
      of {{ totalItems }}
    </div>
    <div class="mx-2">
      <button
        @click="handlePrevPage"
        class="mr-1.5 hover:text-gray-500"
        :class="{
          'text-gray-300 cursor-not-allowed': startItem <= 1,
          'cursor-pointer': startItem > 1,
        }"
        :disabled="startItem <= 1"
      >
        <ChevronLeft class="w-4 h-4" />
      </button>
      <button
        @click="handleNextPage"
        class="hover:text-gray-500"
        :class="{
          'text-gray-300 cursor-not-allowed': endItem > totalItems,
          'cursor-pointer': endItem <= totalItems,
        }"
        :disabled="endItem > totalItems"
      >
        <ChevronRight class="w-4 h-4" />
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuCheckboxItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";
import { ChevronLeft, ChevronRight, ChevronDownIcon } from "lucide-vue-next";
import { onMounted, ref } from "vue";

const props = defineProps<{
  allData: unknown[];
  pageSize: number[];
}>();
const rowData = defineModel<typeof props.allData>();
const emit = defineEmits<{
  (e: "prevPage"): void;
  (e: "nextPage"): void;
}>();
const totalItems = ref<number>(props.allData.length);
const curPageSize = ref<number>(props.pageSize[0] || 10);
const startItem = ref<number>(1);
const endItem = ref<number>(startItem.value + curPageSize.value);

onMounted(() => {
  rowData.value = props.allData.slice(startItem.value - 1, endItem.value - 1);
});

function handleNextPage(): void {
  const newStartItem = startItem.value + curPageSize.value;
  if (newStartItem > totalItems.value) {
    return;
  } else {
    const newEndItem = newStartItem + curPageSize.value;
    if (newEndItem > totalItems.value) {
      startItem.value = newStartItem;
      endItem.value = totalItems.value + 1;
    } else {
      startItem.value = newStartItem;
      endItem.value = newEndItem;
    }
    rowData.value = props.allData.slice(startItem.value - 1, endItem.value - 1);
  }
  emit("nextPage");
}

function handlePrevPage(): void {
  const newStartItem = startItem.value - curPageSize.value;
  if (newStartItem < 0) {
    startItem.value = 1;
    endItem.value = startItem.value + curPageSize.value;
  } else {
    startItem.value = newStartItem;
    endItem.value = startItem.value + curPageSize.value;
  }
  rowData.value = props.allData.slice(startItem.value - 1, endItem.value - 1);
  emit("prevPage");
}

function handlePageSizeChange(newPageSize: number): void {
  curPageSize.value = newPageSize;
  if (startItem.value + curPageSize.value > totalItems.value) {
    endItem.value = totalItems.value + 1;
    if (curPageSize.value > totalItems.value) {
      startItem.value = 1;
    } else {
      startItem.value = totalItems.value - curPageSize.value + 1;
    }
  } else {
    endItem.value = startItem.value + curPageSize.value;
  }
  rowData.value = props.allData.slice(startItem.value - 1, endItem.value - 1);
}
</script>

<style>
.tab-shadow {
  box-shadow: 0 2px 6px #0000001a;
}
</style>
