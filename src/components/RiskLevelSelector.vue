<script setup>
const props = defineProps({
  modelValue: {
    type: String,
    default: 'balanced'
  }
})

const pimentPath = '/assets/piment.svg'
const pimentBlankPath = '/assets/piment_blank.svg'


const emit = defineEmits(['update:modelValue'])

const riskOptions = [
  {
    value: 'Mild',
    label: 'Mild',
    pimentLevel: 1,
    description: 'Lower risk, more stability, and smoother ups and downs.'
  },
  {
    value: 'Tasty',
    label: 'Tasty',
    pimentLevel: 2,
    description: 'A middle ground between growth and protection.'
  },
  {
    value: 'Spicy',
    label: 'Spicy',
    pimentLevel: 4,
    description: 'Higher risk, higher potential growth, and more volatility.'
  },
  {
    value: 'Hot',
    label: 'Hot',
    pimentLevel: 5,
    description: 'A mix of growth and stability for a well-rounded portfolio.'
  },
  {
    value: 'Extra Hot',
    label: 'Extra Hot',
    pimentLevel: 6,
    description: 'A blend of growth and stability for a well-rounded portfolio.'
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

    <div class="grid gap-4 md:grid-cols-3 lg:grid-cols-2">
      <button
        v-for="option in riskOptions"
        :key="option.value"
        type="button"
        class="rounded-2xl border-2 p-4 text-left transition-colors hover:cursor-pointer"
        :class="modelValue === option.value ? 'border-yuh-black bg-yuh-pale-violet' : 'border-yuh-pale-violet bg-white hover:border-yuh-violet'"
        :aria-pressed="modelValue === option.value"
        @click="selectRiskLevel(option.value)"
      >
      <div class="flex flex-row gap-2 items-center">
           <p class="text-lg">{{ option.label }}</p>
        <div>
          <span class="text-yuh-orange font-bold">
            <img :src="pimentPath" :alt="`${option.pimentLevel} piments`" class="inline-block h-4 w-4" v-for="n in option.pimentLevel" :key="n" />
            <img :src="pimentBlankPath" :alt="`${option.pimentLevel} piments`" class="inline-block h-4 w-4" v-for="n in (6 - option.pimentLevel)" :key="n" />
          </span>
        </div>
      </div>
     
        <p class="mt-2 text-sm font-medium text-yuh-black">{{ option.description }}</p>
      </button>
    </div>
  </div>
</template>
