<template>
  <div class="cont chartWrap">
    <div class="legendContainer">
      <div class="legendItem">
        <div class="legendColorBox" style="background-color: rgba(255, 99, 132, 1);"></div>
        <span>Upload</span>
        <div class="legendColorBox" style="background-color: rgba(54, 162, 235, 1);"></div>
        <span>Download</span>
        <div class="legendColorBox" style="background-color: rgba(255, 206, 86, 1);"></div>
        <span>CPU</span>
        <div class="legendColorBox" style="background-color: rgba(75, 192, 192, 1);"></div>
        <span>MEM</span>
      </div>
    </div>

    <canvas ref="canvasEl"></canvas>
  </div>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import Chart from 'chart.js/auto';

export default {
  name: 'MyChart', // 두 단어로

  setup() {
    const canvasEl = ref(null);
    let chart = null;
    let timer = null;
    let dataIndex = 4;

    const createChart = () => {
      const canvas = canvasEl.value;
      if (!canvas) return;
      const ctx = canvas.getContext('2d');

      chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: [0, 1, 2, 3],
          datasets: [
            {
              label: 'Upload',
              data: [0, 0, 0, 0],
              borderColor: 'rgba(255, 99, 132, 1)',
              borderWidth: 1,
              fill: false,
              tension: 0.1,
            },
            {
              label: 'Download',
              data: [0, 0, 0, 0],
              borderColor: 'rgba(54, 162, 235, 1)',
              borderWidth: 1,
              fill: false,
              tension: 0.1,
            },
            {
              label: 'CPU',
              data: [0, 0, 0, 0],
              borderColor: 'rgba(255, 206, 86, 1)',
              borderWidth: 1,
              fill: false,
              tension: 0.1,
            },
            {
              label: 'MEM',
              data: [0, 0, 0, 0],
              borderColor: 'rgba(75, 192, 192, 1)',
              borderWidth: 1,
              fill: false,
              tension: 0.1,
            },
          ],
        },
        options: {
          responsive: true,
          animation: false,
          plugins: {
            legend: { display: false },
            tooltip: { enabled: false },
          },
          scales: {
            x: {
              type: 'linear',
              position: 'bottom',
            },
          },
        },
      });
    };

    const addData = () => {
      if (!chart) return;

      const label = dataIndex;

      if (chart.data.labels.length > 100) {
        chart.data.labels.shift();
        chart.data.datasets.forEach((ds) => ds.data.shift());
      }

      chart.data.labels.push(label);
      chart.data.datasets[0].data.push(Math.sin(label * 0.1) * 10);
      chart.data.datasets[1].data.push(Math.cos(label * 0.1) * 10);
      chart.data.datasets[2].data.push(Math.cos(label * 0.1) * 30);
      chart.data.datasets[3].data.push(Math.sin(label * 0.1) * 30);

      dataIndex += 1;

      chart.update('none');
    };

    onMounted(() => {
      createChart();
      timer = setInterval(addData, 1000);
    });

    onBeforeUnmount(() => {
      if (timer) clearInterval(timer);
      if (chart) {
        chart.destroy();
        chart = null;
      }
    });

    return {
      canvasEl,
    };
  },
};
</script>

<style scoped>
.chartWrap {
  position: relative;
  width: 100%;
  height: 300px;
}
.legendContainer {
  position: static;
  display: flex;
  align-items: flex-start;
  flex-direction: row-reverse;
}
.legendItem {
  display: flex;
  align-items: center;
}
.legendColorBox {
  width: 30px;
  height: 5px;
  margin-left: 20px;
}
</style>
