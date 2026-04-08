<script setup>
const props = defineProps({
  modelValue: {
    type: String,
    default: 'balanced'
  }
})

const emit = defineEmits(['update:modelValue'])

const riskOptions = [
  {
    value: 'prudent',
    label: 'Prudent',
    description: 'Lower risk, more stability, and smoother ups and downs.'
  },
  {
    value: 'balanced',
    label: 'Balanced',
    description: 'A middle ground between growth and protection.'
  },
  {
    value: 'dynamic',
    label: 'Dynamic',
    description: 'Higher risk, higher potential growth, and more volatility.'
  }
]

function selectRiskLevel(value) {
  emit('update:modelValue', value)
}
</script>

<template>
  <div class="flex flex-col gap-8">
    <div class="text-left">
      <p class="text-xl">What is your risk level?</p>
      <p class="mt-1 text-sm font-medium text-yuh-black/70">Choose the option that matches how much market fluctuation you can accept.</p>
    </div>

    <div class="grid gap-4 md:grid-cols-3">
      <button
        v-for="option in riskOptions"
        :key="option.value"
        type="button"
        class="rounded-2xl border-2 p-4 text-left transition-colors hover:cursor-pointer"
        :class="modelValue === option.value ? 'border-yuh-black bg-yuh-pale-violet' : 'border-yuh-pale-violet bg-white hover:border-yuh-violet'"
        :aria-pressed="modelValue === option.value"
        @click="selectRiskLevel(option.value)"
      >
        <p class="text-lg">{{ option.label }}</p>
        <p class="mt-2 text-sm font-medium text-yuh-black">{{ option.description }}</p>
      </button>
    </div>
  </div>
</template>
