<template>
<div>
    <h1>AVM_BILLING</h1>
    <div>
        <h3>[AVM] ADOBE 사용량 측정</h3>
        <!--SQL 입력 영역-->
        <div style="margin: 16px 0;">
            <label>SQL 입력</label>
            <textarea
                v-model="sql"
                rows="4"
                style="width: 100%; font-family: monospace; margin-top: 4px ;"></textarea>
            <button @click="runQuery" style="margin-top: 8px;">실행</button>
            <div v-if="erroMessage" style="color: red; margin-top: 4px;">
                {{ erroMessage }}
            </div>
        </div>

        <!--그리드-->
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
import mockBilling from "@/mocks/billingMockData.json"

ModuleRegistry.registerModules([AllCommunityModule]);

export default {
  name: "App",
  components: {
    AgGridVue,
  },
  setup() {

    // 전체 데이터 (원본)
    const fullData = ref(mockBilling);

    // 실제 그리드에 보여줄 데이터
    const rowData = ref([...fullData.value]);

    // Column Definitions: Defines the columns to be displayed.
    const colDefs = ref([
      { field: "billing_id", headerName: "청구 ID" },
      { field: "billing_date", headerName: "청구일" },
      { field: "billing_month", headerName: "청구월" },
      { field: "customer_name", headerName: "고객명" },
      { field: "service_name", headerName: "서비스" },
      { field: "usage_amount", headerName: "사용량" },
      { field: "unit_price", headerName: "단가" },
      { field: "total_price", headerName: "총 금액" },
      { field: "status", headerName: "상태" },
    ]);

    // sql 입력
    const sql = ref("SELECT * FROM avm_billing;");

    const erroMessage = ref("");

    const runQuery = () => {
      erroMessage.value = "";
      

      const raw = sql.value.trim(); // 앞 뒤 공백 제거하는 함수

      if (!raw) {
        erroMessage.value = "SQL을 입력해주세요.";
        return;
        
      }

      const upper = raw.toUpperCase();

      if (
        upper === "SELECT * FROM AVM_BILLING;" ||
        upper === "SELECT * FROM AVM_BILLING"
      ) {
        rowData.value = [...fullData.value];
        return;
      }

      const whereMatch = raw.match(
        /SELECT\s+\*\s+FROM\s+avm_billing\s+WHERE\s+([\w_]+)\s*=\s*'([^']+)'/i
      );

      if (whereMatch) {
        const column = whereMatch[1];
        const value = whereMatch[2];

        if (!fullData.value.length || !(column in fullData.value[0])) {
          erroMessage.value = `존재하지 않는 컬럼입니다: $(column)`;
          rowData.value = [];
          return;
        }

        rowData.value = fullData.value.filter((row) => {
          return String(row[column]) === value;
        })

        return;
      }

      erroMessage.value =
      "지원하지 않는 SQL 형식입니다.\n예: SELECT * FROM avm_billing WHERE status = 'PAID';";
      rowData.value = [];
    };


    return {
      rowData,
      colDefs,
      sql,
      runQuery,
      erroMessage,
    };
  },
};
</script>

<style>

</style>
