<script setup>
const props = defineProps({
  modelValue: {
    type: String,
    default: 'medium'
  }
})

const emit = defineEmits(['update:modelValue'])

const horizonOptions = [
  {
    value: 'short',
    label: 'Short term',
    description: '0-3 years. You’ll need this money soon.'
  },
  {
    value: 'medium',
    label: 'Medium term',
    description: '3-7 years. You have some time before using this money.'
  },
  {
    value: 'long',
    label: 'Long term',
    description: '7+ years. You won’t need this money for a while.'
  }
]

function selectHorizon(value) {
  emit('update:modelValue', value)
}
</script>

<template>
  <div class="flex flex-col gap-8">
    <div class="text-left">
      <p class="text-xl">When will you need the money that you are investing?</p>
      <p class="mt-1 text-sm font-medium text-yuh-black/70">Choose the timeframe that best matches your goal.</p>
    </div>

    <div class="grid gap-4 md:grid-cols-3">
      <button
        v-for="option in horizonOptions"
        :key="option.value"
        type="button"
        class="rounded-2xl border-2 p-4 text-left transition-colors hover:cursor-pointer"
        :class="modelValue === option.value ? 'border-yuh-black bg-yuh-pale-violet' : 'border-yuh-pale-violet bg-white hover:border-yuh-violet'"
        :aria-pressed="modelValue === option.value"
        @click="selectHorizon(option.value)"
      >
        <p class="text-lg">{{ option.label }}</p>
        <p class="mt-2 text-sm font-medium text-yuh-black">{{ option.description }}</p>
      </button>
    </div>
  </div>
</template>
