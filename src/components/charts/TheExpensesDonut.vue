<script setup>
import { computed, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import Chart from 'chart.js/auto'

const props = defineProps({
    expenses: {
        type: Array,
        default: () => []
    }
})

const palette = ['#FAB4CC', '#CCB1DA', '#EBDDF5', '#BABDE4', '#6197AF', '#FA5B35', '#78C6E7', '#151A21']
const canvasRef = ref(null)
let chartInstance = null
let stopChartWatch = null

function formatCategory(category) {
    const text = String(category || '').replace(/_/g, ' ').trim()
    if (!text) {
        return 'Other'
    }
    return text.charAt(0).toUpperCase() + text.slice(1)
}

const normalizedExpenses = computed(() =>
    (props.expenses || [])
        .map((expense) => ({
            category: String(expense?.category || '').trim(),
            amount: Number(expense?.amount) || 0
        }))
        .filter((expense) => expense.category && expense.amount > 0)
)

const aggregatedExpenses = computed(() => {
    const buckets = new Map()

    normalizedExpenses.value.forEach((expense) => {
        const previous = buckets.get(expense.category) || 0
        buckets.set(expense.category, previous + expense.amount)
    })

    return [...buckets.entries()].map(([category, amount], index) => ({
        key: category,
        label: formatCategory(category),
        amount,
        color: palette[index % palette.length]
    }))
})

const total = computed(() => aggregatedExpenses.value.reduce((sum, item) => sum + item.amount, 0))

const details = computed(() =>
    aggregatedExpenses.value
        .map((item) => ({
            ...item,
            percentage: total.value > 0 ? Math.round((item.amount / total.value) * 100) : 0
        }))
        .sort((a, b) => b.amount - a.amount)
)

function chartData() {
    return {
        labels: aggregatedExpenses.value.map((item) => item.label),
        datasets: [
            {
                data: aggregatedExpenses.value.map((item) => item.amount),
                backgroundColor: aggregatedExpenses.value.map((item) => item.color),
                borderWidth: 0,
                hoverOffset: 4
            }
        ]
    }
}

function createChart() {
    if (!canvasRef.value || aggregatedExpenses.value.length === 0) {
        return
    }

    chartInstance = new Chart(canvasRef.value, {
        type: 'doughnut',
        data: chartData(),
        options: {
            responsive: true,
            maintainAspectRatio: false,
            cutout: '62%',
            plugins: {
                legend: { display: false },
                tooltip: {
                    callbacks: {
                        label(context) {
                            const amount = Number(context.raw) || 0
                            return `${context.label}: CHF ${amount.toLocaleString()}`
                        }
                    }
                }
            }
        }
    })
}

function updateChart() {
    if (!chartInstance) {
        createChart()
        return
    }

    if (aggregatedExpenses.value.length === 0) {
        chartInstance.destroy()
        chartInstance = null
        return
    }

    chartInstance.data = chartData()
    chartInstance.update()
}

stopChartWatch = watch(aggregatedExpenses, () => {
    updateChart()
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
    <div class="w-full max-w-md rounded-xl border border-yuh-pale-violet p-4">
        <p class="mb-3 text-left text-base font-semibold text-yuh-black">Expense breakdown</p>

        <div v-if="details.length === 0" class="text-left text-sm text-yuh-black/70">
            Add at least one expense with an amount greater than 0 to display the chart.
        </div>

        <div v-else class="grid gap-4 md:grid-cols-[180px_1fr] md:items-center">
            <div class="h-44 w-full">
                <canvas ref="canvasRef"></canvas>
            </div>

            <ul class="space-y-2 text-sm">
                <li v-for="item in details" :key="item.key" class="flex items-center justify-between gap-2">
                    <div class="flex items-center gap-2">
                        <span class="inline-block h-3 w-3 rounded-full" :style="{ backgroundColor: item.color }"></span>
                        <span class="text-yuh-black">{{ item.label }}</span>
                    </div>
                    <span class="font-medium text-yuh-black/80">CHF {{ item.amount.toLocaleString() }} ({{
                        item.percentage }}%)</span>
                </li>
            </ul>
        </div>
        <div class="font-medium text-sm flex flex-col items-start gap-2 mt-8">
            <h3 class="font-bold text-md">Are your expenses well balanced?</h3>
            <p class="text-left">
                In our app, you can get personalised recommendations on how to optimize your expenses and save more each month, based on your financial situation and goals.
            </p>
            <a href="https://www.yuh.com/fr/" class="text-yuh-orange hover:text-yuh-black transition-colors">Try it out now !</a>
        </div>
    </div>
</template>

<style scoped></style>