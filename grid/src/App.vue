<template>
<div>
    <h1>AVM_BILLING</h1>
    <div>
        <h3>[AVM] ADOBE 사용량 측정</h3>
        <ag-grid-vue
            style="height: 500px; width: 100%;"
            :rowData="rowData"
            :columnDefs="colDefs"></ag-grid-vue>
    </div>
</div>
</template>

<script>
import { ref } from "vue";
import { AllCommunityModule, ModuleRegistry } from "ag-grid-community";
import { AgGridVue } from "ag-grid-vue3";

ModuleRegistry.registerModules([AllCommunityModule]);

export default {
  name: "App",
  components: {
    AgGridVue,
  },
  setup() {
    // Row Data: The data to be displayed.
    const rowData = ref([
      { make: "Tesla", model: "Model Y", price: 64950, electric: true },
      { make: "Ford", model: "F-Series", price: 33850, electric: true },
      { make: "Toyota", model: "Corolla", price: 29600, electric: true },
      { make: "Toyota", model: "Corolla", price: 29600, electric: true },
    ]);

    // Column Definitions: Defines the columns to be displayed.
    const colDefs = ref([
      { field: "make" },
      { field: "model" },
      { field: "price" },
      { field: "electric" },
    ]);

    return {
      rowData,
      colDefs,
    };
  },
};
</script>

<style>

</style>
