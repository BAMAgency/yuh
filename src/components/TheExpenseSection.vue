<script setup>
import { computed, onMounted } from 'vue'
import TheExpensesSelector from './TheExpensesSelector.vue'

const props = defineProps({
    modelValue: {
        type: Array,
        default: () => []
    }
})

const emit = defineEmits(['update:modelValue'])

const expensesOptions = [
    {
        value: 'rent',
        label: 'Rent',
        description: 'Include your monthly rent or mortgage payments.'
    },
    {
        value: 'utilities',
        label: 'Utilities',
        description: 'Include expenses like electricity, water, and internet.'
    },
    {
        value: 'groceries',
        label: 'Groceries',
        description: 'Include your average monthly spending on food and household items.'
    },
    {
        value: 'transportation',
        label: 'Transportation',
        description: 'Include costs for public transit, fuel, and vehicle maintenance.'
    },
    {
        value: 'entertainment',
        label: 'Entertainment',
        description: 'Include expenses for dining out, movies, and hobbies.'
    },
    {
        value: 'healthcare',
        label: 'Healthcare',
        description: 'Include costs for insurance, medications, and medical visits.'
    },
    {
        value: 'education',
        label: 'Education',
        description: 'Include tuition, books, and other education-related expenses.'
    },
    {
        value: 'miscellaneous',
        label: 'Miscellaneous',
        description: 'Include any other regular expenses not covered by the categories above.'
    },
    {
        value: 'assurance',
        label: 'Assurance',
        description: 'Include costs for insurance policies and related expenses.'
    }
]

const expenses = computed(() => (Array.isArray(props.modelValue) ? props.modelValue : []))
const canAddMore = computed(() => expenses.value.length < expensesOptions.length)

function firstAvailableCategory(usedCategories) {
    return expensesOptions.find((option) => !usedCategories.includes(option.value))?.value || ''
}

function updateExpenses(nextExpenses) {
    emit('update:modelValue', nextExpenses)
}

function addExpense() {
    if (!canAddMore.value) {
        return
    }

    const used = expenses.value.map((expense) => expense.category)
    const nextCategory = firstAvailableCategory(used)

    if (!nextCategory) {
        return
    }

    updateExpenses([...expenses.value, { category: nextCategory, amount: '' }])
}

function removeExpense(index) {
    const nextExpenses = expenses.value.filter((_, expenseIndex) => expenseIndex !== index)
    updateExpenses(nextExpenses)
}

function getOptionsFor(index) {
    const selectedByOthers = new Set(
        expenses.value
            .filter((_, expenseIndex) => expenseIndex !== index)
            .map((expense) => expense.category)
    )

    return expensesOptions.filter((option) => !selectedByOthers.has(option.value))
}

function updateExpense(index, nextExpense) {
    const nextExpenses = [...expenses.value]
    const usedByOthers = nextExpenses
        .filter((_, expenseIndex) => expenseIndex !== index)
        .map((expense) => expense.category)

    let nextCategory = nextExpense.category
    if (usedByOthers.includes(nextCategory)) {
        nextCategory = firstAvailableCategory(usedByOthers)
    }

    nextExpenses[index] = {
        category: nextCategory,
        amount: nextExpense.amount
    }

    updateExpenses(nextExpenses)
}

onMounted(() => {
    if (expenses.value.length === 0) {
        addExpense()
    }
})
</script>

<template>
    <div class="flex flex-col gap-4 border border-yuh-purple/50 rounded-xl p-4">
        <div class="text-left">
            <p class="text-xl">Expense Section</p>
            <p class="mt-1 text-sm font-medium text-yuh-black/70">Add your recurring monthly expenses. You cannot select the same category twice.</p>
        </div>

        <TheExpensesSelector
            v-for="(expense, index) in expenses"
            :key="`expense-${index}`"
            :model-value="expense"
            :options="getOptionsFor(index)"
            :index="index"
            :can-delete="expenses.length > 0"
            @update:model-value="updateExpense(index, $event)"
            @remove="removeExpense(index)"
        />

        <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-3">
            <button
                type="button"
                class="rounded-full bg-yuh-orange px-4 py-2 text-sm font-medium text-white hover:cursor-pointer hover:bg-yuh-black disabled:cursor-not-allowed disabled:opacity-50"
                :disabled="!canAddMore"
                @click="addExpense"
            >
                + Add row
            </button>
            <p class="text-sm text-yuh-black/70">{{ expenses.length }} / {{ expensesOptions.length }} categories selected</p>
        </div>
    </div>
</template>
