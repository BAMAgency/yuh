<script setup>
import { computed, ref } from 'vue'
import { Doughnut } from 'vue-chartjs'
import InfoTooltipButton from '../core/InfoTooltipButton.vue'
import {
    Chart as ChartJS,
    ArcElement,
    Tooltip,
    Legend
} from 'chart.js'

ChartJS.register(ArcElement, Tooltip, Legend)

const props = defineProps({
    left: {
        type: Number,
        default: 0
    },
    debt: {
        type: Number,
        default: 0
    },
    riskLevel: {
        type: String,
        default: 'Tasty'
    },
    objective: {
        type: String,
        default: 'Invest'
    },
    horizon: {
        type: String,
        default: 'medium'
    },
    highInterestDebt: {
        type: Boolean,
        default: false
    },
    save: {
        type: Number,
        default: 0
    },
    monthlyExpenses: {
        type: Number,
        default: 0
    },
    popupIndex: {
        type: Number,
        default: 0
    }
})

const pimentPath = '/assets/piment.svg'
const pimentBlankPath = '/assets/piment_blank.svg'

const riskPimentLevels = {
    Mild: 1,
    Tasty: 2,
    Spicy: 4,
    Hot: 5,
    'Extra Hot': 6
}

const riskPimentLevel = computed(() => riskPimentLevels[props.riskLevel] ?? 2)

const safeLeft = computed(() => Math.max(Number(props.left) || 0, 0))
const safeDebt = computed(() => Math.max(Number(props.debt) || 0, 0))
const safeSavings = computed(() => Math.max(Number(props.save) || 0, 0))
const safeMonthlyExpenses = computed(() => Math.max(Number(props.monthlyExpenses) || 0, 0))


const isAssetmentInfoOpen = ref(false)

const monthsOfSafety = computed(() => {
    if (safeMonthlyExpenses.value <= 0) return 0
    return safeSavings.value / safeMonthlyExpenses.value
})

const recommendedSafetyMonths = computed(() => {
    let target = 3

    if (props.horizon === 'short') target = 6
    else if (props.horizon === 'medium') target = 4
    else if (props.horizon === 'long') target = 3

    if (props.objective === 'Save money') target = Math.max(target, 6)
    if (props.objective === 'Prepare for a major purchase') target = Math.max(target, 4)
    if (props.highInterestDebt) target = Math.max(target, 4)

    return target
})

const emergencyFundGap = computed(() => {
    const targetAmount = recommendedSafetyMonths.value * safeMonthlyExpenses.value
    return Math.max(targetAmount - safeSavings.value, 0)
})

const investmentProfiles = {
    Mild: {
        equities: 20,
        bonds: 65,
        alternatives: 14,
        cash: 1
    },
    Tasty: {
        equities: 40,
        bonds: 48,
        alternatives: 11,
        cash: 1
    },
    Spicy: {
        equities: 60,
        bonds: 32,
        alternatives: 7,
        cash: 1
    },
    Hot: {
        equities: 80,
        bonds: 9,
        alternatives: 10,
        cash: 1
    },
    'Extra Hot': {
        equities: 99,
        bonds: 0,
        alternatives: 0,
        cash: 1
    }
}

const selectedInvestmentProfile = computed(() => {
    return investmentProfiles[props.riskLevel] ?? investmentProfiles.Tasty
})

const allocationPercentages = computed(() => {
    if (safeLeft.value <= 0) {
        return {
            debtRepayment: 0,
            emergencySavings: 0,
            goalSavings: 0,
            investing: 0
        }
    }

    let debtRepayment = 0
    let emergencySavings = 0
    let goalSavings = 0
    let investing = 0

    const safetyMonths = monthsOfSafety.value
    const hasEmergencyGap = emergencyFundGap.value > 0

    if (props.highInterestDebt) {
        if (safetyMonths < 1) {
            debtRepayment = 60
            emergencySavings = 30
            goalSavings = 10
            investing = 0
        } else if (safetyMonths < 3) {
            debtRepayment = 55
            emergencySavings = 25
            goalSavings = 15
            investing = 5
        } else {
            debtRepayment = 50
            emergencySavings = hasEmergencyGap ? 15 : 5
            goalSavings = 15
            investing = hasEmergencyGap ? 20 : 30
        }
    } else {
        if (props.objective === 'Save money') {
            if (safetyMonths < recommendedSafetyMonths.value) {
                emergencySavings = 60
                goalSavings = 30
                investing = 10
            } else {
                emergencySavings = 20
                goalSavings = 60
                investing = 20
            }
        } else if (props.objective === 'Prepare for a major purchase') {
            if (props.horizon === 'short') {
                emergencySavings = hasEmergencyGap ? 30 : 10
                goalSavings = hasEmergencyGap ? 60 : 75
                investing = hasEmergencyGap ? 10 : 15
            } else if (props.horizon === 'medium') {
                emergencySavings = hasEmergencyGap ? 25 : 10
                goalSavings = 50
                investing = hasEmergencyGap ? 25 : 40
            } else {
                emergencySavings = hasEmergencyGap ? 20 : 10
                goalSavings = 35
                investing = hasEmergencyGap ? 45 : 55
            }
        } else if (props.objective === 'Plan for retirement') {
            if (props.horizon === 'short') {
                emergencySavings = hasEmergencyGap ? 35 : 15
                goalSavings = 25
                investing = hasEmergencyGap ? 40 : 60
            } else if (props.horizon === 'medium') {
                emergencySavings = hasEmergencyGap ? 25 : 10
                goalSavings = 15
                investing = hasEmergencyGap ? 60 : 75
            } else {
                emergencySavings = hasEmergencyGap ? 20 : 5
                goalSavings = 10
                investing = hasEmergencyGap ? 70 : 85
            }
        } else {
            // Invest
            if (props.horizon === 'short') {
                emergencySavings = hasEmergencyGap ? 40 : 20
                goalSavings = 40
                investing = hasEmergencyGap ? 20 : 40
            } else if (props.horizon === 'medium') {
                emergencySavings = hasEmergencyGap ? 25 : 10
                goalSavings = 20
                investing = hasEmergencyGap ? 55 : 70
            } else {
                emergencySavings = hasEmergencyGap ? 20 : 5
                goalSavings = 10
                investing = hasEmergencyGap ? 70 : 85
            }
        }

        if (safeDebt.value > 0) {
            const debtBoost = props.highInterestDebt ? 0 : 10
            if (debtBoost > 0) {
                const reducibleFromGoal = Math.min(goalSavings, debtBoost)
                goalSavings -= reducibleFromGoal
                debtRepayment += reducibleFromGoal
            }
        }
    }

    const total = debtRepayment + emergencySavings + goalSavings + investing

    if (total !== 100 && total > 0) {
        const diff = 100 - total
        investing += diff
    }

    return {
        debtRepayment,
        emergencySavings,
        goalSavings,
        investing
    }
})

const allocationAmounts = computed(() => {
    const percentages = allocationPercentages.value

    return {
        debtRepayment: Math.round((safeLeft.value * percentages.debtRepayment) / 100),
        emergencySavings: Math.round((safeLeft.value * percentages.emergencySavings) / 100),
        goalSavings: Math.round((safeLeft.value * percentages.goalSavings) / 100),
        investing: Math.round((safeLeft.value * percentages.investing) / 100)
    }
})

const investmentBreakdownAmounts = computed(() => {
    const investable = Math.max(Number(allocationAmounts.value.investing) || 0, 0)
    const profile = selectedInvestmentProfile.value

    const amounts = {
        equities: Math.round((investable * profile.equities) / 100),
        bonds: Math.round((investable * profile.bonds) / 100),
        alternatives: Math.round((investable * profile.alternatives) / 100),
        cash: Math.round((investable * profile.cash) / 100)
    }

    const distributed = amounts.equities + amounts.bonds + amounts.alternatives + amounts.cash
    const diff = investable - distributed

    amounts.cash += diff
    if (amounts.cash < 0) {
        amounts.cash = 0
    }

    return amounts
})

const mainLegendItems = computed(() => [
    {
        key: 'debtRepayment',
        label: 'Debt repayment',
        amount: allocationAmounts.value.debtRepayment,
        percentage: allocationPercentages.value.debtRepayment,
        color: '#FA5B35'
    },
    {
        key: 'emergencySavings',
        label: 'Emergency fund',
        amount: allocationAmounts.value.emergencySavings,
        percentage: allocationPercentages.value.emergencySavings,
        color: '#6197AF'
    },
    {
        key: 'goalSavings',
        label: 'Goal savings',
        amount: allocationAmounts.value.goalSavings,
        percentage: allocationPercentages.value.goalSavings,
        color: '#FAB4CC'
    },
    {
        key: 'investing',
        label: 'Investing',
        amount: allocationAmounts.value.investing,
        percentage: allocationPercentages.value.investing,
        color: '#CCB1DA'
    }
])

const investmentLegendItems = computed(() => [
    {
        key: 'equities',
        label: 'Equities',
        percentage: selectedInvestmentProfile.value.equities,
        amount: investmentBreakdownAmounts.value.equities,
        color: '#CCB1DA'
    },
    {
        key: 'bonds',
        label: 'Bonds',
        percentage: selectedInvestmentProfile.value.bonds,
        amount: investmentBreakdownAmounts.value.bonds,
        color: '#6197AF'
    },
    {
        key: 'alternatives',
        label: 'Alternatives',
        percentage: selectedInvestmentProfile.value.alternatives,
        amount: investmentBreakdownAmounts.value.alternatives,
        color: '#FAB4CC'
    },
    {
        key: 'cash',
        label: 'Cash',
        percentage: selectedInvestmentProfile.value.cash,
        amount: investmentBreakdownAmounts.value.cash,
        color: '#EBDDF5'
    }
])

const mainChartData = computed(() => ({
    labels: mainLegendItems.value.map((item) => item.label),
    datasets: [
        {
            data: mainLegendItems.value.map((item) => item.amount),
            backgroundColor: mainLegendItems.value.map((item) => item.color),
            borderWidth: 0
        }
    ]
}))

const investmentChartData = computed(() => ({
    labels: investmentLegendItems.value.map((item) => item.label),
    datasets: [
        {
            data: investmentLegendItems.value.map((item) => item.percentage),
            backgroundColor: investmentLegendItems.value.map((item) => item.color),
            borderWidth: 0
        }
    ]
}))

const chartOptions = {
    responsive: true,
    maintainAspectRatio: false,
    cutout: '68%',
    plugins: {
        legend: {
            display: false
        },
        tooltip: {
            callbacks: {
                label(context) {
                    const value = context.raw || 0
                    return `${context.label}: CHF ${Number(value).toLocaleString()}`
                }
            }
        }
    }
}

const investmentChartOptions = {
    responsive: true,
    maintainAspectRatio: false,
    cutout: '68%',
    plugins: {
        legend: {
            display: false
        },
        tooltip: {
            callbacks: {
                label(context) {
                    const value = context.raw || 0
                    return `${context.label}: ${value}%`
                }
            }
        }
    }
}

const recommendationText = computed(() => {
    if (safeLeft.value <= 0) {
        return 'No monthly amount is currently available for saving or investing after expenses.'
    }

    if (props.highInterestDebt) {
        return 'High-interest debt is prioritized before investing aggressively, because its cost can outweigh potential market returns.'
    }

    if (monthsOfSafety.value < recommendedSafetyMonths.value) {
        return 'Part of the recommendation is directed to emergency savings first, to build a stronger financial buffer.'
    }

    if (props.horizon === 'short') {
        return 'Because the goal is short term, the recommendation favors cash savings over market exposure.'
    }

    if (props.horizon === 'long') {
        return 'Because the goal is long term, the recommendation allocates more to investing, where short-term volatility is easier to absorb.'
    }

    return 'The recommendation balances safety, flexibility and long-term growth based on the profile provided.'
})
</script>

<template>
    <div class="flex w-full flex-col gap-6 lg:flex-row lg:items-start">
        <div class="rounded-2xl border border-yuh-purple p-5 text-left">
            <div class="mb-4">
                <h2 class="text-2xl font-semibold text-yuh-black" :class="props.popupIndex === 2 ? 'animate-bounce' : ''">
                    Recommended monthly allocation
                </h2>
                <p class="text-sm font-medium text-yuh-black/70">
                    Suggested breakdown of your monthly amount left after expenses
                </p>
            </div>

            <div v-if="left > 0" class="grid gap-6 lg:grid-row-[320px_1fr]">
                <div class=" p-4">
                    <div class="h-80">
                        <Doughnut :data="mainChartData" :options="chartOptions" />
                    </div>
                    <ul class="mt-4 space-y-2 text-sm">
                        <li v-for="item in mainLegendItems" :key="item.key"
                            class="flex items-center justify-between gap-2">
                            <div class="flex items-center gap-2">
                                <span class="inline-block h-3 w-3 rounded-full"
                                    :style="{ backgroundColor: item.color }"></span>
                                <span class="text-yuh-black">{{ item.label }}</span>
                            </div>
                            <span class="font-medium text-yuh-black/80">CHF {{ item.amount.toLocaleString() }} ({{
                                item.percentage }}%)</span>
                        </li>
                    </ul>
                </div>

                <div id="allocation-details" class="flex flex-col gap-4">
                    <div class="grid gap-3 sm:grid-cols-2">
                        <div class="rounded-xl bg-yuh-pale-violet p-4">
                            <p class="text-sm font-medium text-yuh-black/70">Debt repayment</p>
                            <p class="text-2xl font-bold text-yuh-black">{{ allocationAmounts.debtRepayment }} CHF</p>
                            <p class="text-sm text-yuh-black/70">{{ allocationPercentages.debtRepayment }}%</p>
                        </div>

                        <div class="rounded-xl bg-yuh-pale-violet p-4">
                            <p class="text-sm font-medium text-yuh-black/70">Emergency fund</p>
                            <p class="text-2xl font-bold text-yuh-black">{{ allocationAmounts.emergencySavings }} CHF
                            </p>
                            <p class="text-sm text-yuh-black/70">{{ allocationPercentages.emergencySavings }}%</p>
                        </div>

                        <div class="rounded-xl bg-yuh-pale-violet p-4">
                            <p class="text-sm font-medium text-yuh-black/70">Goal savings</p>
                            <p class="text-2xl font-bold text-yuh-black">{{ allocationAmounts.goalSavings }} CHF</p>
                            <p class="text-sm text-yuh-black/70">{{ allocationPercentages.goalSavings }}%</p>
                        </div>

                        <div class="rounded-xl bg-yuh-pale-violet p-4">
                            <p class="text-sm font-medium text-yuh-black/70">Investing</p>
                            <p class="text-2xl font-bold text-yuh-black">{{ allocationAmounts.investing }} CHF</p>
                            <p class="text-sm text-yuh-black/70">{{ allocationPercentages.investing }}%</p>
                        </div>
                    </div>

                    <div>
                        <button @click="isAssetmentInfoOpen = !isAssetmentInfoOpen"
                            class="text-sm text-yuh-orange hover:text-yuh-black transition-colors mb-2">
                            {{ isAssetmentInfoOpen ? '- Show less' : '+ Why this split?' }}
                        </button>
                    </div>
                    <div v-if="isAssetmentInfoOpen">
                        <div class="rounded-xl border border-yuh-pale-violet p-4">
                        <p class="text-base font-semibold text-yuh-black">Why this split?</p>
                        <p class="mt-2 text-sm font-medium text-yuh-black/70">
                            {{ recommendationText }}
                        </p>
                    </div>

                    <div v-if="isAssetmentInfoOpen" class="rounded-xl border border-yuh-pale-violet p-4">
                        <p class="text-base font-semibold text-yuh-black">Emergency savings status</p>
                        <div class="mt-3 grid gap-3 sm:grid-cols-3">
                            <div>
                                <p class="text-sm font-medium text-yuh-black/70">Current savings</p>
                                <p class="text-xl font-bold text-yuh-black">{{ save }} CHF</p>
                            </div>
                            <div>
                                <p class="text-sm font-medium text-yuh-black/70">Months covered</p>
                                <p class="text-xl font-bold text-yuh-black">{{ monthsOfSafety.toFixed(1) }}</p>
                            </div>
                            <div>
                                <p class="text-sm font-medium text-yuh-black/70">Recommended target</p>
                                <p class="text-xl font-bold text-yuh-black">{{ recommendedSafetyMonths }} months</p>
                            </div>
                        </div>
                    </div>
                    </div>
                </div>
            </div>

            <div v-else class="rounded-xl bg-yuh-pale-violet p-4 text-sm font-medium text-yuh-black/70">
                No investable monthly amount is available right now. The priority is to reduce expenses or improve cash
                flow first.
            </div>
        </div>

        <div v-if="allocationAmounts.investing > 0" class="rounded-2xl border border-yuh-purple p-5 text-left">
            <div class="mb-4">
                <h2 class="text-2xl font-semibold text-yuh-black"  :class="props.popupIndex === 3 ? 'animate-bounce' : ''">Suggested investment mix</h2>
                <p class="text-sm font-medium text-yuh-black/70">
                    Breakdown of the investing portion based on the selected risk level
                </p>
            </div>
            <div class="flex flex-col">
                <div class="flex w-full items-center gap-2">
                    <div>
                        <p class="text-base font-bold text-yuh-black">
                            Selected risk level : <span class="font-bold text-yuh-orange">{{ riskLevel }}</span>
                        </p>
                    </div>
                    <div class="ml-auto">
                        <InfoTooltipButton text="This affects only the composition of the investing portion, not the emergency savings or
                            debt priorities." />
                    </div>

                </div>
                <div class="text-yuh-orange">
                    <img v-for="n in riskPimentLevel" :key="`piment-${n}`" :src="pimentPath"
                        :alt="`${riskPimentLevel} piments`" class="inline-block h-6 w-6" />
                    <img v-for="n in 6 - riskPimentLevel" :key="`piment-blank-${n}`" :src="pimentBlankPath"
                        :alt="`${riskPimentLevel} piments`" class="inline-block h-6 w-6" />
                </div>
            </div>

            <div class="grid gap-6 lg:grid-row-[320px_1fr]">
                <div class="p-4">
                    <div class="h-80">
                        <Doughnut :data="investmentChartData" :options="investmentChartOptions" />
                    </div>
                    <ul class="mt-4 space-y-2 text-sm">
                        <li v-for="item in investmentLegendItems" :key="item.key"
                            class="flex items-center justify-between gap-2">
                            <div class="flex items-center gap-2">
                                <span class="inline-block h-3 w-3 rounded-full"
                                    :style="{ backgroundColor: item.color }"></span>
                                <span class="text-yuh-black">{{ item.label }}</span>
                            </div>
                            <span class="font-medium text-yuh-black/80">{{ item.percentage }}% · CHF {{
                                item.amount.toLocaleString() }}</span>
                        </li>
                    </ul>
                </div>

                <div id="investment-allocation-details" class="flex flex-col gap-4">
                    <div class="rounded-xl bg-yuh-pale-violet p-4">
                        <p class="text-sm font-medium text-yuh-black/70">Monthly amount to invest</p>
                        <p class="text-2xl font-bold text-yuh-black">{{ allocationAmounts.investing }} CHF</p>
                    </div>

                    <div class="grid gap-3 sm:grid-cols-2">
                        <div class="rounded-xl bg-yuh-pale-violet p-4">
                            <p class="text-sm font-medium text-yuh-black/70">Equities</p>
                            <p class="text-2xl font-bold text-yuh-black">CHF {{
                                investmentBreakdownAmounts.equities.toLocaleString() }}</p>
                            <p class="text-sm text-yuh-black/70">{{ selectedInvestmentProfile.equities }}%</p>
                        </div>
                        <div class="rounded-xl bg-yuh-pale-violet p-4">
                            <p class="text-sm font-medium text-yuh-black/70">Bonds</p>
                            <p class="text-2xl font-bold text-yuh-black">CHF {{
                                investmentBreakdownAmounts.bonds.toLocaleString() }}</p>
                            <p class="text-sm text-yuh-black/70">{{ selectedInvestmentProfile.bonds }}%</p>
                        </div>

                        <div class="rounded-xl bg-yuh-pale-violet p-4">
                            <p class="text-sm font-medium text-yuh-black/70">Alternatives</p>
                            <p class="text-2xl font-bold text-yuh-black">CHF {{
                                investmentBreakdownAmounts.alternatives.toLocaleString() }}</p>
                            <p class="text-sm text-yuh-black/70">{{ selectedInvestmentProfile.alternatives }}%</p>
                        </div>

                        <div class="rounded-xl bg-yuh-pale-violet p-4">
                            <p class="text-sm font-medium text-yuh-black/70">Cash</p>
                            <p class="text-2xl font-bold text-yuh-black">CHF {{
                                investmentBreakdownAmounts.cash.toLocaleString() }}</p>
                            <p class="text-sm text-yuh-black/70">{{ selectedInvestmentProfile.cash }}%</p>
                        </div>
                    </div>


                </div>
            </div>
        </div>
    </div>
</template>

<style scoped></style>