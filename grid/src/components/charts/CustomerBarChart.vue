<template>
    <div class="chart-card">
        <h3>주요 고객사별 매출</h3>
        <div class="chart-body">
            <canvas ref="canvasEl"></canvas>
        </div>
    </div>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import Chart from 'chart.js/auto'
import billingData from '../../mocks/billingMockData.json'

export default {
  name: 'CustomerBarChart',
  setup() {
    const canvasEl = ref(null)
    let chartInstance = null

    // 👉 1) 고객사별 매출 합산
    const totalsByCustomer = {}

    billingData.forEach((row) => {
      const customer = row.customer_name || '기타'
      const price = row.total_price || 0
      if (!totalsByCustomer[customer]) {
        totalsByCustomer[customer] = 0
      }
      totalsByCustomer[customer] += price
    })

    // 👉 2) 매출 기준 내림차순 정렬 후 TOP 5
    const sorted = Object.entries(totalsByCustomer) // [ [name, total], ... ]
      .sort((a, b) => b[1] - a[1])
      .slice(0, 5)

    const labels = sorted.map(([name]) => name)
    const data = sorted.map(([, total]) => total)

    onMounted(() => {
      const ctx = canvasEl.value.getContext('2d')

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
            y: { beginAtZero: true },
          },
          plugins: {
            legend: { display: false },
          },
        },
      })
    })

    onBeforeUnmount(() => {
      if (chartInstance) chartInstance.destroy()
    })

    return { canvasEl }
  },
}
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