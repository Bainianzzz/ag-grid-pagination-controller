<template>
  <div class="flex p-2 pl-3 pb-1 m-3.5 border rounded tab-shadow w-fit">
    <div class="text-sm mr-1">
      <DropdownMenu>
        <DropdownMenuTrigger asChild>
          <span class="mx-2">
            {{ startItem }}-{{ endItem - 1 }}
            <ChevronDownIcon class="w-4 h-4 inline" />
          </span>
        </DropdownMenuTrigger>
        <DropdownMenuContent class="w-fit">
          <DropdownMenuLabel>Page size</DropdownMenuLabel>
          <DropdownMenuSeparator />
          <DropdownMenuCheckboxItem
            @select="handlePageSizeChange(10)"
            :disabled="pageSize === 10"
          >
            10
          </DropdownMenuCheckboxItem>
          <DropdownMenuCheckboxItem
            @select="handlePageSizeChange(20)"
            :disabled="pageSize === 20"
            v-if="totalItems > 20"
          >
            20
          </DropdownMenuCheckboxItem>
          <DropdownMenuCheckboxItem
            @select="handlePageSizeChange(50)"
            :disabled="pageSize === 50"
            v-if="totalItems > 50"
          >
            50
          </DropdownMenuCheckboxItem>
          <DropdownMenuCheckboxItem
            @select="handlePageSizeChange(100)"
            :disabled="pageSize === 100"
            v-if="totalItems > 100"
          >
            100
          </DropdownMenuCheckboxItem>
        </DropdownMenuContent>
      </DropdownMenu>
      of {{ totalItems }}
    </div>
    <div class="mx-1">
      <button @click="handlePrevPage" class="mr-1">
        <ChevronLeft class="w-4 h-4" />
      </button>
      <button @click="handleNextPage">
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
  allData: any;
  pageSize: number;
}>();
const rowData = defineModel<typeof props.allData>();
const totalItems = ref(props.allData.length);
const pageSize = ref(props.pageSize || 10);
const startItem = ref(1);
const endItem = ref(startItem.value + pageSize.value);

onMounted(() => {
  rowData.value = props.allData.slice(startItem.value - 1, endItem.value - 1);
});

function handleNextPage() {
  const newStartItem = startItem.value + pageSize.value;
  if (newStartItem > totalItems.value) {
    return;
  } else {
    const newEndItem = newStartItem + pageSize.value;
    if (newEndItem > totalItems.value) {
      startItem.value = newStartItem;
      endItem.value = totalItems.value + 1;
    } else {
      startItem.value = newStartItem;
      endItem.value = newEndItem;
    }
    rowData.value = props.allData.slice(startItem.value - 1, endItem.value - 1);
  }
}

function handlePrevPage() {
  const newStartItem = startItem.value - pageSize.value;
  if (newStartItem < 0) {
    startItem.value = 1;
    endItem.value = startItem.value + pageSize.value;
  } else {
    startItem.value = newStartItem;
    endItem.value = startItem.value + pageSize.value;
  }
  rowData.value = props.allData.slice(startItem.value - 1, endItem.value - 1);
}

function handlePageSizeChange(newPageSize: number) {
  pageSize.value = newPageSize;
  if (startItem.value + pageSize.value > totalItems.value) {
    endItem.value = totalItems.value + 1;
  } else {
    endItem.value = startItem.value + pageSize.value;
  }
  rowData.value = props.allData.slice(startItem.value - 1, endItem.value - 1);
}
</script>

<style>
.tab-shadow {
  box-shadow: 0 2px 6px #0000001a;
}
</style>
