<template>
  <div class="chart-card">
    <h3>일자별 매출 추이</h3>
    <div class="chart-body"><canvas ref="canvasEl"></canvas></div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import Chart from 'chart.js/auto'

const canvasEl = ref(null)
let chartInstance = null

onMounted(() => {
  const ctx = canvasEl.value.getContext('2d')


  const labels = ['2025-12-01', '2025-12-02', '2025-12-03', '2025-12-04']
  const data = [18000, 120000, 90000, 60000] 

  chartInstance = new Chart(ctx, {
    type: 'line',
    data: {
      labels,
      datasets: [
        {
          label: '총 매출(원)',
          data,
          tension: 0.3,
          fill: false,
        },
      ],
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        y: {
          beginAtZero: true,
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
