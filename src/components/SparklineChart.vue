<template>
  <div class="chart-container">
    <LineChart :data="chartData" :options="chartOptions" />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { Line as LineChart } from 'vue-chartjs';
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Tooltip
} from 'chart.js';

// Registrasi modul Chart.js
ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement, Tooltip);

const props = defineProps<{
  history: number[];
  isPositive: boolean;
}>();

// Konfigurasi Data Chart
const chartData = computed(() => ({
  labels: ['1', '2', '3', '4', '5', '6', '7'], // Label dummy 7 titik
  datasets: [
    {
      data: props.history,
      borderColor: props.isPositive ? '#28a745' : '#dc3545', // Hijau jika naik, Merah jika turun
      borderWidth: 2,
      pointRadius: 0, // Hilangkan titik agar murni garis (sparkline)
      fill: false,
      tension: 0.4 // Membuat garis sedikit melengkung (smooth)
    }
  ]
}));

// Konfigurasi Opsi Chart (Menghilangkan grid, axis, dll)
const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { display: false },
    tooltip: { enabled: false }
  },
  scales: {
    x: { display: false }, // Sembunyikan sumbu X
    y: { display: false }  // Sembunyikan sumbu Y
  },
  animation: false as const // Matikan animasi agar list tidak lag saat di-scroll
};
</script>

<style scoped>
.chart-container {
  height: 40px; /* Tinggi grafik */
  width: 100%;
}
</style>
