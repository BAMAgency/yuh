<script setup>
import { ref } from 'vue'

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

const totalExpenses = ref(props.expenses.reduce((total, expense) => total + (expense.amount || 0), 0))

const getLeftAmount = () => {
    console.log('income', props.income)
    console.log('expenses', props.expenses)

    let leftAmount = props.income - totalExpenses.value;

    if(leftAmount < 0) {
        return 0
    }
    
    return leftAmount;
}

const financialHealth = () => {
    if (!props.income || props.income <= 0) return 0

    const leftAmount = props.income - totalExpenses.value
    const safeLeftAmount = leftAmount < 0 ? 0 : leftAmount

    const expenseRatio = totalExpenses.value / props.income
    const debtRatio = props.debt / props.income
    const savingsBuffer = totalExpenses.value > 0 ? props.save / totalExpenses.value : 0
    const investmentRatio = props.currentInvestment / (props.income * 12)

    const leftAmountRate = safeLeftAmount / props.income

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
}
</script>

<template>
    <!--
      - revenus mensuels 
      - dépenses mensuelles 
      - reste disponible
      - montant conseillé à garder en sécurité 
      - montant conseillé à investir
      - répartition conseillée du portefeuille
      - signalement en cas de problème (ex: si le reste disponible est négatif, si certaines dépenses dépassent les recommandations, etc.)
      - proposition d'analyse avec l'agent IA si besoin
    -->

<div>
 <h1>Left amount</h1>
 <p>{{ getLeftAmount() }}</p>
 <h1>Financial health</h1>
    <p>{{ financialHealth() }}</p>
 <p></p>
</div>
</template>

<style scoped>

</style>
