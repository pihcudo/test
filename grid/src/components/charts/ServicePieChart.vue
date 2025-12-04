<template>
  <div class="chart-card">
    <h3>서비스별 매출 비중</h3>
    <div class="chart-body">
        <canvas ref="canvasEl"></canvas>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import Chart from 'chart.js/auto'

const canvasEl = ref(null)
let chartInstance = null

onMounted(() => {
  const ctx = canvasEl.value.getContext('2d')


  const labels = ['API', 'DASHBOARD', 'STORAGE']
  const data = [250000, 180000, 90000] 

  chartInstance = new Chart(ctx, {
    type: 'pie',
    data: {
      labels,
      datasets: [
        {
          data,
        },
      ],
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: {
          position: 'left',
        },
      },
    },
  })
})

onBeforeUnmount(() => {
  if (chartInstance) {
    chartInstance.destroy()
  }
})
</script>

<style>
.chart-card {
  padding: 16px 24px;
  border-radius: 12px;
  border: 1px solid #e3e3e3;
  background: #fff;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.chart-body {
    position: relative;
    width: 100%;
    height: 220px;
}

.chart-body canvas {
    width: 100% !important;
    height: 100% !important;
}

.chart-card h3 {
  font-size: 14px;
  font-weight: 600;
}
</style>
