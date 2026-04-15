<script setup>
import { ref } from 'vue'

defineProps({
  text: {
    type: String,
    required: true
  }
})

const isOpen = ref(false)

function toggle() {
  isOpen.value = !isOpen.value
}

function open() {
  isOpen.value = true
}

function close() {
  isOpen.value = false
}
</script>

<template>
  <div class="relative">
    <button
      type="button"
      class="flex h-5 w-5 items-center justify-center rounded-full border-2 border-yuh-black text-xs text-yuh-black hover:cursor-pointer hover:bg-yuh-purple"
      :aria-expanded="isOpen"
      aria-label="Show information"
      @click.stop="toggle"
      @mouseenter="open"
      @mouseleave="close"
      @focus="open"
      @blur="close"
    >
i
    </button>
    <div
      v-show="isOpen"
      class="absolute bottom-full right-0 mb-2 w-72 rounded-md bg-yuh-black p-3 whitespace-normal text-[0.7rem] font-medium leading-snug text-white sm:w-80 sm:text-xs md:w-96 md:text-sm"
    >
      {{ text }}
    </div>
  </div>
</template>
