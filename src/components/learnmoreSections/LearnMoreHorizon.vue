<script setup>
import { ref } from 'vue'
import YouTubeThumbnail from './YouTubeThumbnail.vue'


const isOpen = ref(false)

function toggle() {
  isOpen.value = !isOpen.value
}
</script>

<template>
  <div class="rounded-2xl border-2 border-yuh-pale-violet">
    <button type="button" class="flex w-full items-center justify-between px-6 py-4 text-yuh-black hover:cursor-pointer"
      :aria-expanded="isOpen" aria-controls="horizon-help-panel" @click="toggle">
      <div class="flex w-full items-center gap-2">
        <span class="font-bold">How to choose your investment horizon</span>
        <span class="ml-auto text-sm font-bold text-yuh-orange">YuhLearn {{ isOpen ? 'x' : '+' }}</span>
      </div>
    </button>

    <div v-if="isOpen" id="horizon-help-panel" class="px-6 pb-5 text-left text-sm font-medium text-yuh-black">
      <p class="mb-2">
        Your investment horizon is how long you plan to keep your money invested before using it.
      </p>
      <ul class="list-disc space-y-1 pl-5">
        <li><span class="font-bold">Short term:</span> if you’ll need the money within the next 3 years</li>
        <li><span class="font-bold">Medium term:</span> if your goal is 3 to 7 years away</li>
        <li><span class="font-bold">Long term:</span> if you don’t need the money for at least 7 years</li>
      </ul>
      <p class="mt-3">
        The longer your horizon, the more short-term fluctuations you can typically accept in exchange for higher potential returns.      </p>
        <h4 class="font-bold text-md mt-4">
          Not sure?
        </h4>
          <p>Think about when you’ll realistically need this money. You can always adjust your strategy as your situation changes.</p>
      <div class="mt-2">
        <div class="mt-3">
          <YouTubeThumbnail video-id="UTSsOaRJQs4" title="Need help choosing your investment horizon?"
            href="https://www.youtube.com/watch?v=UTSsOaRJQs4" />
        </div>
      </div>
    </div>

  </div>
</template>
