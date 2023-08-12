<template>
  <div class="chart-container">
    <canvas ref="lineChartCanvas"></canvas>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';
import { Chart, registerables } from 'chart.js';

// Register necessary plugins
Chart.register(...registerables);

defineProps<{
  companyName: String,
  graph: Object,
}>();

const lineChartCanvas = ref(null);
let lineChart: Chart | null = null;

onMounted(() => {
  if (lineChartCanvas.value) {
    const context = (lineChartCanvas.value as any).getContext('2d');
    if (context) {
      lineChart = new Chart(context, {
        type: 'line',
        data: {
          labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
          datasets: [
            {
              label: 'Sample Line Chart',
              data: [10, 20, 30, 25, 15, 40, 35],
              backgroundColor: 'rgba(75, 192, 192, 0.2)',
              borderColor: 'rgba(75, 192, 192, 1)',
              borderWidth: 1,
            },
          ],
        },
        options: {
          scales: {
            y: {
              beginAtZero: true,
            },
          },
        },
      });
    }
  }
});

// Cleanup when the component is unmounted
onUnmounted(() => {
  if (lineChart) {
    lineChart.destroy();
    lineChart = null;
  }
});
</script>

<style scoped>
.chart-container {
  width: 80%;
  max-width: 800px;
  margin: 0 auto;
}
</style>
