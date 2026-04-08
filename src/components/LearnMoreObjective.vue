<script setup>
import { ref } from 'vue'

const isOpen = ref(false)

function toggle() {
  isOpen.value = !isOpen.value
}
</script>

<template>
  <div class="rounded-2xl border-2 border-yuh-pale-violet">
    <button
      type="button"
      class="flex w-full font-medium items-center justify-between px-6 py-4 text-yuh-black hover:cursor-pointer"
      :aria-expanded="isOpen"
      aria-controls="objective-help-panel"
      @click="toggle"
    >
      <span>How to set an objective ?</span>
      <span class="text-sm">{{ isOpen ? '^' : 'v' }}</span>
    </button>

    <div v-if="isOpen" id="objective-help-panel" class="px-6 pb-5 text-left text-sm text-yuh-black font-medium">
      <p class="mb-2">
        Pick the objective that matches what you need most in the next months.
      </p>
      <ul class="list-disc space-y-1 pl-5">
        <li>Save money: build a safety buffer for short-term needs.</li>
        <li>Invest: grow your capital over the long term.</li>
        <li>Major purchase: prepare for a car, studies, or a home project.</li>
        <li>Retirement: prioritize stability and long-term planning.</li>
      </ul>
    </div>
  </div>
</template>
