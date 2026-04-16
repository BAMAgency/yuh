<script setup>
import { computed, ref } from 'vue'
import TheExpensesDonut from './charts/TheExpensesDonut.vue'
import TheInvestmentDonut from './charts/TheInvestmentDonut.vue'
import { Download } from 'lucide-vue-next';
import { ChevronRight } from 'lucide-vue-next';

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
    highInterestDebt: Boolean,
})

const isOpen = ref(false);
// const isMonthlyExpensesDetailsOpen = ref(false);
// const isFinancialHealthDetailsOpen = ref(false);

const safeIncome = computed(() => Number(props.income) || 0)
const safeDebt = computed(() => Number(props.debt) || 0)
const safeCurrentInvestment = computed(() => Number(props.currentInvestment) || 0)
const safeSave = computed(() => Number(props.save) || 0)

const popupIndex = ref(0);

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
      - montant conseillé à garder en sécurité -> fait
      - montant conseillé à investir -> fait
      - répartition conseillée du portefeuille -> fait
      - signalement en cas de problème (ex: si le reste disponible est négatif, si certaines dépenses dépassent les recommandations, etc.)
      - proposition d'analyse avec l'agent IA si bsesoin -> fait
      - possibilité d'exporter les résultats et les recommandations -> à faire
    -->

    <div id="chatbot-button" class="fixed right-4 sm:right-6" style="bottom: calc(1rem + env(safe-area-inset-bottom));">

        <button type="button" @click="popupIndex = 0"
            class="flex h-14 w-14 sm:h-16 sm:w-16 items-center justify-center rounded-full border-4 bg-white border-yuh-orange shadow-lg transition-transform hover:scale-105 hover:cursor-pointer">

            <img src="/assets/yuhlia.svg" alt="Yuh chatbot" class="h-14 w-14 sm:h-16 sm:w-16" />
        </button>
    </div>

    <div class="flex flex-col gap-4 items-center">

        <h1 class="font-bold text-4xl text-yuh-black">
            Your financial recommendations
        </h1>
        <p class="font-medium mb-6">
            Here are your personalized financial recommendations based on your income and expenses.
        </p>
        <div class="flex w-full justify-end ">
            <div
                class="flex flex-row items-center gap-2 font-bold text-yuh-orange hover:underline p-2 rounded-xl hover:cursor-pointer text-xs">
                <Download /> Export my recommendations
            </div>
        </div>

        <!-- Left Amount -->
        <div id="left-amount"
            class="relative overflow-visible w-full lg:min-w-xl flex flex-col gap-4 rounded-xl border border-yuh-purple p-4 text-left">
            <div v-if="popupIndex === 0" class="yuhlia-greeting absolute z-20 -translate-y-1/2 -translate-x-1/2">
                <div
                    class="yuhlia-soft-bounce  bg-yuh-orange text-white p-2 rounded-2xl max-w-sm flex flex-row items-center">
                    <img class="h-80 w-80  -translate-x-5 translate-y-10" src="/assets/yuhlia-entire.svg" alt="Yuhlia" />
                    <button @click="popupIndex = 12"
                        class="text-white absolute top-2 left-2 rounded-full  w-6 h-6 flex items-center justify-center">
                        X
                    </button>
                    <div class="flex flex-col items-end gap-16 mr-2">
                        <h2 class="font-medium text-sm mr-4">1/4</h2>
                        <h1 class="text-lg font-bold">Hi ! I am Yuhlia, your personal AI financial assistant!</h1>
                        <button
                            class="flex flex-row items-center text-black bg-white rounded-2xl text-orange-yuh px-4 py-2 font-bold transition-colors  hover:cursor-pointer"
                            @click="popupIndex++">
                            <span class="mr-4">Next</span>
                            <ChevronRight />
                        </button>
                    </div>
                </div>
            </div>
            
            <div
                id="left-amount-details"
                class="grid items-center gap-4 md:grid-cols-[auto_1fr]"
            >
                <div class="relative inline-flex items-center justify-center">
                    <p class="relative z-10 text-4xl font-bold leading-none text-yuh-violet md:text-5xl">
                        {{ getLeftAmount() }} CHF
                    </p>
                </div>
                <div  class="flex flex-col gap-2">
                    <h1 class="text-2xl font-semibold text-yuh-black md:text-3xl">Left amount</h1>
                    <p class="text-sm font-medium text-yuh-black/70 md:text-base">
                        What's left after expenses each month
                    </p>
                </div>
            </div>
            <div v-if="popupIndex === 1" class="yuhlia-greeting absolute z-20 translate-y-2/3 translate-x-3/3">
                <div
                    class="yuhlia-soft-bounce  bg-yuh-orange text-white p-2 rounded-2xl max-w-xs flex flex-row items-center">
                    <button @click="popupIndex = 12"
                        class="text-white absolute top-2 left-2 rounded-full  w-6 h-6 flex items-center justify-center ">
                        X
                    </button>
                    <div class="flex flex-col items-center gap-6 mx-2">
                        <h2 class="font-medium text-sm">2/4</h2>
                        <h1 class="text-lg font-bold">Your left Amount</h1>
                        <p class="font-medium text-center">
                            This is the amount you have left each month after covering your expenses. It is calculated by subtracting your total monthly expenses from your monthly income.
                        </p>
                        <button
                            class="mb-2 flex flex-row items-center text-black bg-white rounded-2xl text-orange-yuh px-4 py-2 font-bold transition-colors  hover:cursor-pointer"
                            @click="popupIndex++">
                            <span class="mr-4 ">Next</span>
                            <ChevronRight />
                        </button>
                    </div>
                </div>
            </div>
            <div>
                <button @click="isOpen = !isOpen"
                    class="text-sm text-yuh-orange hover:text-yuh-black transition-colors mb-2">
                    {{ isOpen ? '- Show less' : '+ Show expenses details' }}
                </button>
            </div>
            <div v-show="isOpen">
                <TheExpensesDonut :expenses="expenses" :income="income" />
            </div>
        </div>

        <!--Months of security
        <div class="w-full lg:min-w-xl flex flex-col gap-4 rounded-xl border border-yuh-purple p-4 text-left">
            <div class="grid items-center gap-4 md:grid-cols-[auto_1fr] ">
                <p class="text-4xl font-bold leading-none text-yuh-violet md:text-5xl">
                    {{ monthSecurityAmount }} months
                </p>
                <div class="flex flex-col gap-1">
                    <h1 class="text-2xl font-semibold text-yuh-black md:text-3xl">Months of security</h1>
                    <p class="text-sm font-medium text-yuh-black/70 md:text-base">
                        How long your savings can cover your monthly expenses
                    </p>
                </div>
            </div>
            <div class="w-full">
                <button @click="isMonthlyExpensesDetailsOpen = !isMonthlyExpensesDetailsOpen"
                    class="mb-2 text-sm text-yuh-orange transition-colors hover:text-yuh-black">
                    {{ isMonthlyExpensesDetailsOpen ? '- Show less' : '+ What does that result mean?' }}
                </button>
            </div>
            <div v-show="isMonthlyExpensesDetailsOpen" class="w-full text-left">
                <p class="text-sm text-yuh-black/70">This is calculated by dividing your total savings by your total
                    monthly expenses. It indicates how many months you could cover your expenses using only your
                    savings, without any additional income.</p>
            </div>
        </div>-->

        <!--Financial Health index
        <div
            class="w-full flex flex-col items-center gap-2 rounded-xl border border-yuh-purple p-4 text-center lg:min-w-xl">
            <h1 class="text-4xl">Financial health Index</h1>
            <TheSpeedometerChart :value="financialHealth" label="Financial health" />
            <p class="text-sm text-yuh-black font-medium text-center">Financial health score based on income,
                expenses,<br />
                debt, savings and investments</p>

            <div class="w-full text-left">
                <button @click="isFinancialHealthDetailsOpen = !isFinancialHealthDetailsOpen"
                    class="mb-2 text-sm text-yuh-orange transition-colors hover:text-yuh-black">
                    {{ isFinancialHealthDetailsOpen ? '- Show less' : '+ What is that index?' }}
                </button>
            </div>
            <div v-show="isFinancialHealthDetailsOpen" class="w-full text-left">
                <p class="text-sm text-yuh-black/70">This index provides a comprehensive view of your financial
                    well-being by evaluating key aspects of your financial situation.</p>
            </div>
        </div>-->

        <!--The investment recommandations chart-->
        <TheInvestmentDonut :left="leftAmount" :debt="debt" :riskLevel="riskLevel" :objective="objective"
            :horizon="horizon" :highInterestDebt="highInterestDebt" :save="save" :monthlyExpenses="totalExpenses" />
        <div class="font-medium text-sm">
            <p>This is for informational purposes only. Yuh takes no responsibility for any financial decisions you make
                based on these recommendations.</p>
        </div>
    </div>
</template>

<style scoped>
.yuhlia-greeting {
    left: 0;
    top: 0;
}

.yuhlia-greeting__card {
    position: relative;
    z-index: 2;
    box-shadow: 0 12px 26px rgba(0, 0, 0, 0.18);
}

.yuhlia-soft-bounce {
    animation: yuhliaSoftBounce 1.2s ease-in-out infinite;
}

@keyframes yuhliaSoftBounce {

    0%,
    100% {
        transform: translateY(0);
    }

    50% {
        transform: translateY(-6px);
    }
}
</style>
