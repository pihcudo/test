<template>
    <div class="chart-card">
        <h3>서비스별 매출 비중</h3>
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
        name: 'ServicePieChart',
        setup() {
            const canvasEl = ref(null)
            let chartInstance = null

            const totalsByService = {}

            billingData.forEach((row) => {
                const service = row.service_name || '기타'
                const price = row.total_price || 0
                if (!totalsByService[service]) {
                    totalsByService[service] = 0
                }
                totalsByService[service] += price
            })

            const labels = Object.keys(totalsByService)
            const data = labels.map((k) => totalsByService[k])

            onMounted(() => {
                const ctx = canvasEl
                    .value
                    .getContext('2d')

                chartInstance = new Chart(ctx, {
                    type: 'pie',
                    data: {
                        labels,
                        datasets: [{
                                data
                            }]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: {
                            legend: {
                                position: 'left'
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