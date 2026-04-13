<script setup>
import { ChevronDown } from 'lucide-vue-next'

const props = defineProps({
  modelValue: {
    type: String,
    default: ''
  },
  id: {
    type: String,
    required: true
  },
  name: {
    type: String,
    required: true
  },
  label: {
    type: String,
    required: true
  },
  options: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['update:modelValue'])

function onChange(event) {
  emit('update:modelValue', event.target.value)
  }
</script>

<template>
  <div class="flex flex-col gap-3">
    <label class="text-xl" :for="id">{{ label }}</label>
    <div class="relative">
      <select
        :id="id"
        :name="name"
        class="yuh-select"
        :value="modelValue"
        @change="onChange"
      >
        <option v-for="option in options" :key="option" :value="option">
          {{ option }}
        </option>
      </select>
      <span class="pointer-events-none absolute inset-y-0 right-3 flex items-center text-yuh-orange">
        <ChevronDown class="h-4 w-4" />
      </span>
    </div>
  </div>
</template>

<style scoped>
.yuh-select {
  width: 100%;
  -webkit-appearance: none;
  appearance: none;
  border: 0;
  border-radius: 9999px;
  background: #ebddf5;
  padding: 0.65rem 2.25rem 0.65rem 0.9rem;
  color: #151a21;
  font-weight: 500;
  outline: none;
  transition: box-shadow 0.2s ease, background-color 0.2s ease;
}

.yuh-select:hover {
  background: #ccb1da;
}

.yuh-select:focus-visible {
  box-shadow: 0 0 0 3px rgba(120, 198, 231, 0.55);
}
</style>
