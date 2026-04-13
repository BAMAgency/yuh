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
    expenses: Array
})  

const getLeftAmount = () => {
    console.log('income', props.income)
    console.log('expenses', props.expenses)

    const totalExpenses = props.expenses.reduce((total, expense) => total + expense.amount, 0)
    return props.income - totalExpenses
}

const financialHealth = () => {
    const leftAmount = getLeftAmount()
    if (leftAmount < 0) {
        return 'Your expenses exceed your income. Consider reducing your expenses or increasing your income.'
    } else if (leftAmount < props.income * 0.2) {
        return 'Your left amount is quite low. Try to save more or reduce your expenses.'
    } else {
        return 'Your financial health looks good! Keep it up!'
    }
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
 <p></p>
</div>
</template>

<style scoped>

</style>
