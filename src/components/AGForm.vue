<template>
  <ag-grid-vue :rowData="rowData" :columnDefs="colDefs" style="height: 500px; width: 50%">
  </ag-grid-vue>
  <paging-tab
    :allData="Data"
    :pageSize=10
    v-model="rowData"
  />
</template>

<script setup lang="ts">
import { AllCommunityModule, ModuleRegistry } from "ag-grid-community";
ModuleRegistry.registerModules([AllCommunityModule]);
import { AgGridVue } from "ag-grid-vue3";
import { Data } from "@/fakedata/data";
import { ref } from "vue";
import PagingTab from "@/components/PagingTab.vue";

const rowData = ref(Data);

// Column Definitions: Defines the columns to be displayed.
const colDefs = ref([
  { field: "make" },
  { field: "model" },
  { field: "price" },
  { field: "electric" },
]);
</script>
