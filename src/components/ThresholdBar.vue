<template>
  <div class="container">
    <div class="glitch animated-1">
      <canvas height="100%" id="hydroponic-ph-data"></canvas>
    </div>
    <div class="glitch animated-1">
      <canvas height="100%" id="hydroponic-tds-data"></canvas>
    </div>
    <div class="glitch animated-1">
      <canvas height="100%" id="hydroponic-temperature-data"></canvas>
    </div>
  </div>
  <svg height="0">
    <filter id="filter">
      <feColorMatrix type="matrix" result="red_" values="4 0 0 0 0
              0 0 0 0 0
              0 0 0 0 0
              0 0 0 1 0" />
      <feOffset in="red_" dx="1" dy="0" result="red" />
      <feColorMatrix type="matrix" in="SourceGraphic" result="blue_" values="0 0 0 0 0
              0 3 0 0 0
              0 0 10 0 0
              0 0 0 1 0" />
      <feOffset in="blue_" dx="-1" dy="0" result="blue" />
      <feBlend mode="screen" in="red" in2="blue" />
    </filter>
  </svg>
  <svg height="0">
    <filter id="filter2">
      <feColorMatrix type="matrix" result="red_" values="4 0 0 0 0
              0 0 0 0 0
              0 0 0 0 0
              0 0 0 1 0" />
      <feOffset in="red_" dx="-.5" dy="-.5" result="red" />
      <feColorMatrix type="matrix" in="SourceGraphic" result="blue_" values="0 0 0 0 0
              0 3 0 0 0
              0 0 10 0 0
              0 0 0 1 0" />
      <feOffset in="blue_" dx=".5" dy=".5" result="blue" />
      <feBlend mode="screen" in="red" in2="blue" />
    </filter>
  </svg>
</template>

<script setup>
import { onBeforeMount, onMounted, ref } from "vue";

import Chart from 'chart.js/auto';

const waterData = ref([]);
onBeforeMount(async () => {
  waterData.value = await fetchData();
});

onMounted(() => {
  const phChart = new Chart(
    document.getElementById('hydroponic-ph-data').getContext('2d'),
    {
      type: 'line',
      data: {
        labels: [waterData.value.timestamp],
        datasets: [
          {
            label: 'PH',
            data: Array(waterData.value.ph),
            fill: false,
            borderColor: 'rgb(75, 192, 192)',
            tension: 0.1
          }
        ]
      },
      options: {
        scales: {
          y: {
            beginAtZero: true
          }
        }
      }
    }
  );

  const tdsChart = new Chart(
    document.getElementById('hydroponic-tds-data').getContext('2d'),
    {
      type: 'line',
      data: {
        labels: [waterData.value.timestamp],
        datasets: [
          {
            label: 'TDS',
            data: Array(waterData.value.tds),
            fill: false,
            borderColor: 'rgb(75, 192, 192)',
            tension: 0.1
          }
        ]
      },
      options: {
        scales: {
          y: {
            beginAtZero: true
          }
        }
      }
    }
  );

  const temperatureChart = new Chart(
    document.getElementById('hydroponic-temperature-data').getContext('2d'),
    {
      type: 'line',
      data: {
        labels: [waterData.value.timestamp],
        datasets: [
          {
            label: 'Temperature',
            data: Array(waterData.value.temperature),
            fill: false,
            borderColor: 'rgb(75, 192, 192)',
            tension: 0.1
          }
        ]
      },
      options: {
        scales: {
          y: {
            beginAtZero: true
          }
        }
      }
    }
  );

  const interval = setInterval(async () => {
    const newData = await fetchData();
    phChart.data.datasets[0].data = Array(...phChart.data.datasets[0].data.concat(newData.ph));
    phChart.data.labels = Array(...phChart.data.labels.concat(newData.timestamp));
    phChart.update();

    tdsChart.data.datasets[0].data = Array(...tdsChart.data.datasets[0].data.concat(newData.tds));
    tdsChart.data.labels = Array(...tdsChart.data.labels.concat(newData.timestamp));
    tdsChart.update();

    temperatureChart.data.datasets[0].data = Array(...temperatureChart.data.datasets[0].data.concat(newData.temperature));
    temperatureChart.data.labels = Array(...temperatureChart.data.labels.concat(newData.timestamp));
    temperatureChart.update();

  }, 1000);
  return () => clearInterval(interval);
});

async function fetchData() {

  return {
    ph: Math.random() * 14,
    tds: Math.random() * 2000,
    temperature: Math.random() * 40,
    timestamp: new Date().toLocaleTimeString()
  };
}

</script>

<style scoped>
.glitch {
  filter: url("#filter");
}

@keyframes noise-anim-1 {
  0% {
    filter: url("#filter2");
  }

  15% {
    filter: url("#filter");
  }

  34% {
    filter: url("#filter2");
  }

  43% {
    filter: url("#filter2");
  }

  52% {
    filter: url("#filter");
  }

  71% {
    filter: url("#filter2");
  }


}

/* glitch animation with thinner bars */



@keyframes noise-anim-2 {
  0% {
    clip-path: inset(40% 0 61% 0);
  }

  20% {
    clip-path: inset(92% 0 90% 0);
  }

  40% {
    clip-path: inset(43% 0 20% 0);
  }

  60% {
    clip-path: inset(25% 0 58% 0);
  }

  80% {
    clip-path: inset(54% 0 7% 0);
  }

  100% {
    clip-path: inset(0% 0 80% 0);
  }
}

.animated-1 {
  animation: noise-anim-1 .35s infinite linear alternate-reverse;
}

.animated-2 {
  animation: noise-anim-2 .35s infinite linear alternate-reverse;
}

.container {
  display: grid;
  grid-template-columns: repeat(2, 50vw);
  grid-template-rows: repeat(2, 50vh);
}
</style>
