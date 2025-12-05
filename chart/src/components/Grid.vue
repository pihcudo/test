<!-- gpt에서 찾아낸 실시간 데이터 업로드 방법 -->

<template>
  <div class="cont">
    <div class="chartWrap">
      <canvas ref="chartCanvas"></canvas>
    </div>

    <!-- 실시간 데이터 테이블 -->
    <table class="dataTable">
      <thead>
        <tr>
          <th>시간</th>
          <th>Upload</th>
          <th>Download</th>
          <th>CPU</th>
          <th>MEM</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in tableRows" :key="row.time">
          <td>{{ row.time }}</td>
          <td>{{ row.upload }}</td>
          <td>{{ row.download }}</td>
          <td>{{ row.cpu }}</td>
          <td>{{ row.mem }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import Chart from 'chart.js/auto';
// import axios from 'axios'; // 진짜 API 쓸 때

export default {
  name: 'RealtimeChart',

  setup() {
    const chartCanvas = ref(null);
    const tableRows = ref([]);

    let chart = null;      // ✅ 반응형 아님
    let timerId = null;    // ✅ 반응형 아님
    let index = 0;         // ✅ 반응형 아님

    // 1) 실시간 데이터 가져오기 (지금은 가짜 데이터)
    const fetchRealtimeData = async () => {
      // 진짜 API 예시
      // const res = await axios.get('/api/realtime');
      // const d = res.data;

      const t = index;
      return {
        time: t,
        upload:   Math.sin(t * 0.1) * 10,
        download: Math.cos(t * 0.1) * 10,
        cpu:      Math.cos(t * 0.1) * 30,
        mem:      Math.sin(t * 0.1) * 30,
      };
    };

    // 2) 차트 + 테이블 동시 업데이트
    const fetchAndUpdate = async () => {
      if (!chart) return;

      const data = await fetchRealtimeData();

      if (chart.data.labels.length > 100) {
        chart.data.labels.shift();
        chart.data.datasets.forEach((ds) => ds.data.shift());
        if (tableRows.value.length > 0) {
          tableRows.value.shift();
        }
      }

      chart.data.labels.push(data.time);
      chart.data.datasets[0].data.push(data.upload);
      chart.data.datasets[1].data.push(data.download);
      chart.data.datasets[2].data.push(data.cpu);
      chart.data.datasets[3].data.push(data.mem);
      chart.update('none');

      tableRows.value.push(data);
      index++;
    };

    onMounted(() => {
      const canvas = chartCanvas.value;
      if (!canvas) return;

      const ctx = canvas.getContext('2d');

      // ✅ Chart 인스턴스를 여기서만 생성하고, 반응형에 안 올림
      chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: [],
          datasets: [
            {
              label: 'Upload',
              data: [],
              borderColor: 'rgba(255, 99, 132, 1)',
              borderWidth: 1,
              fill: false,
              tension: 0.1,
            },
            {
              label: 'Download',
              data: [],
              borderColor: 'rgba(54, 162, 235, 1)',
              borderWidth: 1,
              fill: false,
              tension: 0.1,
            },
            {
              label: 'CPU',
              data: [],
              borderColor: 'rgba(255, 206, 86, 1)',
              borderWidth: 1,
              fill: false,
              tension: 0.1,
            },
            {
              label: 'MEM',
              data: [],
              borderColor: 'rgba(75, 192, 192, 1)',
              borderWidth: 1,
              fill: false,
              tension: 0.1,
            },
          ],
        },
        options: {
          plugins: {
            legend: { display: true },
          },
          scales: {
            x: {
              type: 'linear',
              position: 'bottom',
            },
          },
        },
      });

      timerId = setInterval(fetchAndUpdate, 1000);
    });

    onBeforeUnmount(() => {
      if (timerId) clearInterval(timerId);
      if (chart) {
        chart.destroy();
        chart = null;
      }
    });

    return {
      chartCanvas,
      tableRows,
    };
  },
};
</script>

<style>
.chartWrap {
  width: 100%;
  height: 300px;
}
.dataTable {
  margin-top: 20px;
  border-collapse: collapse;
  width: 100%;
}
.dataTable th,
.dataTable td {
  border: 1px solid #ddd;
  padding: 4px 8px;
  font-size: 12px;
}
</style>
