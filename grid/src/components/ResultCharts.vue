<template>
  <div class="charts-wrapper">
    <LineChart />
    <ServicePieChart />
    <CustomerBarChart />
  </div>
</template>

<script setup>
import LineChart from './charts/LineChart.vue'
import ServicePieChart from './charts/ServicePieChart.vue'
import CustomerBarChart from './charts/CustomerBarChart.vue'
</script>

<style scoped>
.charts-wrapper {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 16px;
}

/* 좁은 화면 대응 */
@media (max-width: 1024px) {
  .charts-wrapper {
    grid-template-columns: repeat(1, minmax(0, 1fr));
  }
}
</style>
