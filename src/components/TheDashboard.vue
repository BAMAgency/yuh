<script setup>
import { computed, ref } from 'vue'
import TheSpeedometerChart from './charts/TheSpeedometerChart.vue'
import TheExpensesDonut from './charts/TheExpensesDonut.vue'

const props = defineProps({
    age: Number,
    income: Number,
    currentInvestment: Number,
    debt: Number,
    objective: String,
    horizon: String,
    riskLevel: String,
    expenses: Array,
    save: Number,
})

const isOpen = ref(false);

const safeIncome = computed(() => Number(props.income) || 0)
const safeDebt = computed(() => Number(props.debt) || 0)
const safeCurrentInvestment = computed(() => Number(props.currentInvestment) || 0)
const safeSave = computed(() => Number(props.save) || 0)

const totalExpenses = computed(() =>
    (props.expenses || []).reduce((total, expense) => total + (Number(expense.amount) || 0), 0)
)

const leftAmount = computed(() => {
    const result = safeIncome.value - totalExpenses.value
    return result < 0 ? 0 : result
})

const getLeftAmount = () => {
    return leftAmount.value
}

const monthSecurityAmount = computed(() => {
    const monthlyExpenses = totalExpenses.value
    if (monthlyExpenses <= 0) {
        return 0
    }
    const monthsCovered = safeSave.value / monthlyExpenses
    return Math.round(monthsCovered * 100) / 100
})

const financialHealth = computed(() => {
    if (safeIncome.value <= 0) return 0

    const rawLeftAmount = safeIncome.value - totalExpenses.value
    const safeLeftAmount = rawLeftAmount < 0 ? 0 : rawLeftAmount

    const expenseRatio = totalExpenses.value / safeIncome.value
    const debtRatio = safeDebt.value / safeIncome.value
    const savingsBuffer = totalExpenses.value > 0 ? safeSave.value / totalExpenses.value : 0
    const investmentRatio = safeCurrentInvestment.value / (safeIncome.value * 12)

    const leftAmountRate = safeLeftAmount / safeIncome.value

    const leftAmountScore =
        leftAmountRate >= 0.2 ? 100 :
            leftAmountRate >= 0.1 ? 70 :
                leftAmountRate > 0 ? 40 : 0

    const expenseScore =
        expenseRatio <= 0.7 ? 100 :
            expenseRatio <= 0.9 ? 60 :
                expenseRatio <= 1 ? 30 : 0

    const debtScore =
        debtRatio <= 0.3 ? 100 :
            debtRatio <= 0.6 ? 60 :
                debtRatio <= 1 ? 30 : 0

    const savingsBufferScore =
        savingsBuffer >= 6 ? 100 :
            savingsBuffer >= 3 ? 70 :
                savingsBuffer >= 1 ? 40 : 0

    const investmentScore =
        investmentRatio >= 2 ? 100 :
            investmentRatio >= 1 ? 70 :
                investmentRatio > 0 ? 40 : 0

    const score =
        0.3 * leftAmountScore +
        0.25 * expenseScore +
        0.2 * debtScore +
        0.15 * savingsBufferScore +
        0.1 * investmentScore

    return Math.round(score)
})
</script>

<template>
    <!--
      - revenus mensuels -> fait
      - dépenses mensuelles -> fait
      - reste disponible -> fait 
      - montant conseillé à garder en sécurité 
      - montant conseillé à investir
      - répartition conseillée du portefeuille
      - signalement en cas de problème (ex: si le reste disponible est négatif, si certaines dépenses dépassent les recommandations, etc.)
      - proposition d'analyse avec l'agent IA si bsesoin
    -->

    <div class="flex flex-col gap-12 items-center">
        <div class="flex flex-row items-start gap-2 align-middle">
            <p class="text-4xl text-yuh-violet">{{ getLeftAmount() }} CHF</p>

            <div class="flex flex-col items-start">
                <div>
                    <h1 class="text-xl">Left amount</h1>
                </div>
                <div>
                    <p class="text-md text-yuh-black font-medium text-left">What's left after expenses each month</p>
                </div>
                <div>
                    <button @click="isOpen = !isOpen"
                        class="text-sm text-yuh-orange hover:text-yuh-black transition-colors mb-2">
                        {{ isOpen ? 'Show less' : 'Show expenses details' }}
                    </button>
                </div>
                <div v-show="isOpen">
                    <TheExpensesDonut :expenses="expenses" />
                </div>
            </div>
        </div>

        <div class="mt-6 flex flex-col gap-6">
            <h1 class="text-xl">Financial health</h1>
            <TheSpeedometerChart :value="financialHealth" label="Financial health" />
            <p class="text-md text-yuh-black font-medium text-center">Financial health score based on income, expenses,
                debt, savings and investments</p>
        </div>
        <div class="flex flex-col gap-2">
            <h1 class="text-xl">Months of security</h1>
            <p class="text-md text-yuh-black font-medium">Months covered by security amount.
            </p>
            <p class="text-2xl text-yuh-violet">{{ monthSecurityAmount }}</p>
        </div>
    </div>
</template>

<style scoped></style>
