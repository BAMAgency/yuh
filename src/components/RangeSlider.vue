<script setup>
import { computed, ref } from 'vue'

const props = defineProps({
  modelValue: {
    type: Number,
    required: true
  },
  id: {
    type: String,
    default: 'range-slider'
  },
  name: {
    type: String,
    default: 'range-slider'
  },
  label: {
    type: String,
    default: 'Value'
  },
  min: {
    type: Number,
    default: 0
  },
  max: {
    type: Number,
    default: 100
  },
  step: {
    type: Number,
    default: 1
  },
  info: {
    type: String,
    default:""
  }
})

const emit = defineEmits(['update:modelValue'])
const isInfoOpen = ref(false)

const sliderBackground = computed(() => {
  const range = props.max - props.min
  const progress = range === 0 ? 0 : ((props.modelValue - props.min) / range) * 100

  return {
    background: `linear-gradient(to right, #fa5b35 0%, #fa5b35 ${progress}%, #ebddf5 ${progress}%, #ebddf5 100%)`
  }
})

function onInput(event) {
  emit('update:modelValue', Number(event.target.value))
}

function toggleInfo() {
  isInfoOpen.value = !isInfoOpen.value
}

function openInfo() {
  isInfoOpen.value = true
}

function closeInfo() {
  isInfoOpen.value = false
}
</script>

<template>
  <div class="flex flex-col gap-3">
    <div class="flex flex-row items-center gap-2 text-xl">
      <label :for="id">{{ label }}:</label>
      <div class="font-medium">{{ modelValue }}</div>
      <div class="ml-auto">
        <div
          v-if="info"
          class="relative"
        >
          <button
            type="button"
            class="flex h-5 w-5 items-center justify-center rounded-full bg-yuh-pale-violet text-xs text-gray-700 hover:cursor-pointer"
            :aria-expanded="isInfoOpen"
            aria-label="Show information"
            @click.stop="toggleInfo"
            @mouseenter="openInfo"
            @mouseleave="closeInfo"
            @focus="openInfo"
            @blur="closeInfo"
          >
            i
          </button>
          <div
            v-show="isInfoOpen"
            class="font-medium absolute bottom-full right-0 mb-2 w-max rounded-md bg-yuh-black p-2 text-xs text-white"
          >
            {{ info }}
          </div>
        </div>
      </div>
    </div>
    <input
      :id="id"
      :name="name"
      class="yuh-slider"
      :style="sliderBackground"
      type="range"
      :min="min"
      :max="max"
      :step="step"
      :value="modelValue"
      @input="onInput"
    />
  </div>
</template>

<style scoped>
.yuh-slider {
  -webkit-appearance: none;
  appearance: none;
  width: 100%;
  height: 0.5rem;
  border-radius: 9999px;
  background: #ebddf5;
  outline: none;
}

.yuh-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 1.25rem;
  height: 1.25rem;
  border-radius: 9999px;
  background: #fa5b35;
  box-shadow: 0 2px 8px rgba(21, 26, 33, 0.2);
  cursor: pointer;
}

.yuh-slider::-moz-range-thumb {
  width: 1.25rem;
  height: 1.25rem;
  border-radius: 9999px;
  border: 2px solid #fa5b35;
  background: #fa5b35;
  box-shadow: 0 2px 8px rgba(21, 26, 33, 0.2);
  cursor: pointer;
}

.yuh-slider::-moz-range-track {
  height: 0.5rem;
  border-radius: 9999px;
  background: #ebddf5;
}

.yuh-slider::-moz-range-progress {
  height: 0.5rem;
  border-radius: 9999px;
  background: #fa5b35;
}

.yuh-slider:focus-visible {
  box-shadow: 0 0 0 3px rgba(120, 198, 231, 0.55);
}
</style>
