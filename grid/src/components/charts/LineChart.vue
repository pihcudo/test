<template>
    <div class="chart-card">
        <h3>일자별 매출 추이</h3>
        <div class="chart-body">
            <canvas ref="canvasEl"></canvas>
        </div>
    </div>
</template>

<script>
    import {ref, onMounted, onBeforeUnmount} from 'vue'
    import Chart from 'chart.js/auto'
    import billingData from '../../mocks/billingMockData.json'

    export default {
        name: 'LineChart',
        setup() {
            const canvasEl = ref(null)
            let chartInstance = null

            const totalsByDate = {}

            billingData.forEach((row) => {
                const date = row.billing_date
                const price = row.total_price || 0
                if (!totalsByDate[date]) {
                    totalsByDate[date] = 0
                }
                totalsByDate[date] += price
            })

            const labels = Object
                .keys(totalsByDate)
                .sort()
            const data = labels.map((d) => totalsByDate[d])

            onMounted(() => {
                const ctx = canvasEl
                    .value
                    .getContext('2d')

                chartInstance = new Chart(ctx, {
                    type: 'line',
                    data: {
                        labels,
                        datasets: [
                            {
                                label: '총 매출(원)',
                                data,
                                tension: 0.3
                            }
                        ]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        scales: {
                            y: {
                                beginAtZero: true
                            }
                        }
                    }
                })
            })

            onBeforeUnmount(() => {
                if (chartInstance) 
                    chartInstance.destroy()
            })

            return {canvasEl}

        }
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