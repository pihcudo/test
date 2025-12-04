<template>
  <div class="chart-card">
    <h3>주요 고객사별 매출</h3>
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


  const labels = ['테슬라코리아', '포드모터스', '현대자동차', '기아']
  const data = [180000, 220000, 150000, 130000]

  chartInstance = new Chart(ctx, {
    type: 'bar',
    data: {
      labels,
      datasets: [
        {
          label: '총 매출(원)',
          data,
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
      plugins: {
        legend: {
          display: false,
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
