<script setup>
import { computed, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import Chart from 'chart.js/auto'


const props = defineProps({
  value: {
    type: Number,
    default: 0
  },
  label: {
    type: String,
    default: 'Score'
  }
})

const canvasRef = ref(null)
let chartInstance = null
let stopChartWatch = null

const clampedValue = computed(() => {
  if (!Number.isFinite(props.value)) {
    return 0
  }
  return Math.max(0, Math.min(100, Math.round(props.value)))
})

const gaugeColor = computed(() => {
  if (clampedValue.value >= 70) return 'rgb(250, 180, 204)'
  if (clampedValue.value >= 40) return 'rgb(120, 198, 231)'
  return 'rgb(250, 91, 53)'
})

function createChart() {
  if (!canvasRef.value) {
    return
  }

  chartInstance = new Chart(canvasRef.value, {
    type: 'doughnut',
    data: {
      datasets: [
        {
          data: [clampedValue.value, 100 - clampedValue.value],
          backgroundColor: [gaugeColor.value, 'rgb(234, 234, 234)'],
          borderWidth: 0,
          cutout: '72%'
        }
      ]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      rotation: -90,
      circumference: 180,
      animation: {
        duration: 400
      },
      plugins: {
        legend: {
          display: false
        },
        tooltip: {
          enabled: false
        }
      }
    }
  })
}

stopChartWatch = watch([clampedValue, gaugeColor], () => {
  if (!chartInstance) {
    return
  }

  chartInstance.data.datasets[0].data = [clampedValue.value, 100 - clampedValue.value]
  chartInstance.data.datasets[0].backgroundColor = [gaugeColor.value, 'rgb(234, 234, 234)']
  chartInstance.update()
})

onMounted(() => {
  createChart()
})

onBeforeUnmount(() => {
  if (stopChartWatch) {
    stopChartWatch()
    stopChartWatch = null
  }

  if (chartInstance) {
    chartInstance.destroy()
    chartInstance = null
  }
})
</script>

<template>
  <div class="w-full max-w-sm">
    <div class="relative h-44 w-full">
      <canvas ref="canvasRef"></canvas>
      <div class="pointer-events-none absolute inset-0 flex flex-col items-center justify-center pt-9 text-center">
        <p class="text-4xl font-display" :style="{ color: gaugeColor }">{{ clampedValue }}%</p>
        <p class="text-sm font-medium text-yuh-black/70">{{ label }}</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>