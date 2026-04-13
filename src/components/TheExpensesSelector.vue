<script setup>
import { ChevronDown } from 'lucide-vue-next'

const props = defineProps({
    modelValue: {
        type: Object,
        default: () => ({ category: '', amount: '' })
    },
    options: {
        type: Array,
        default: () => []
    },
    index: {
        type: Number,
        default: 0
    },
    canDelete: {
        type: Boolean,
        default: true
    }
})

const emit = defineEmits(['update:modelValue', 'remove'])

function updateCategory(event) {
    emit('update:modelValue', {
        ...props.modelValue,
        category: event.target.value
    })
}

function updateAmount(event) {
    emit('update:modelValue', {
        ...props.modelValue,
        amount: event.target.value
    })
}

function removeExpense() {
    emit('remove')
}
</script>

<template>
    <div :id="`expense-section-${index}`"
        class="flex flex-col md:flex-row gap-3  pb-4 md:grid-cols-[1.2fr_1fr_auto] md:items-center border-b border-yuh-purple/50">
        <div class="text-left relative">
            <select :id="`expense-category-${index}`" class="yuh-select" :value="modelValue.category"
                @change="updateCategory" placeholder="Category">
                <option v-for="option in options" :key="option.value" :value="option.value">
                    {{ option.label }}
                </option>
            </select>
            <span class="pointer-events-none absolute inset-y-0 right-3 flex items-center text-yuh-orange">
                <ChevronDown class="h-4 w-4" />
            </span>
        </div>

        <div class="text-left">
            <input :id="`expense-amount-${index}`" type="number" min="0" class="yuh-input"
                placeholder="Your monthly expense (CHF)" :value="modelValue.amount" @input="updateAmount" />
        </div>

        <button type="button"
            class="justify-self-end rounded-full border border-yuh-orange px-3 py-2 text-xs font-semibold uppercase tracking-wide text-yuh-orange hover:cursor-pointer hover:bg-yuh-orange hover:text-white disabled:cursor-not-allowed disabled:opacity-50"
            :disabled="!canDelete" @click="removeExpense">
            Delete
        </button>
    </div>
</template>

<style scoped>
.yuh-select,
.yuh-input {
    width: 100%;
    border: 0;
    border-radius: 0.75rem;
    background: #ebddf5;
    padding: 0.55rem 0.75rem;
    color: #151a21;
    font-size: 0.92rem;
    font-weight: 500;
    outline: none;
    transition: box-shadow 0.2s ease, background-color 0.2s ease;
}

.yuh-select {
    -webkit-appearance: none;
    appearance: none;
}

.yuh-select:hover,
.yuh-input:hover {
    background: #ccb1da;
}

.yuh-select:focus-visible,
.yuh-input:focus-visible {
    box-shadow: 0 0 0 3px rgba(120, 198, 231, 0.55);
}
</style>
