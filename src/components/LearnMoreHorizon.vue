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
        <span class="font-bold">How to set an horizon ?</span>
        <span class="ml-auto text-sm font-bold text-yuh-orange">YuhLearn {{ isOpen ? '^' : 'v' }}</span>
      </div>
    </button>

    <div v-if="isOpen" id="horizon-help-panel" class="px-6 pb-5 text-left text-sm font-medium text-yuh-black">
      <p class="mb-2">
        Your horizon is the amount of time you plan to keep your money invested before you need it.
      </p>
      <ul class="list-disc space-y-1 pl-5">
        <li><span class="font-bold">Short term:</span> for goals you want to reach soon, usually less than 3 years.</li>
        <li><span class="font-bold">Medium term:</span> for goals in the next 3 to 7 years.</li>
        <li><span class="font-bold">Long term:</span> for goals that are 7 years away or more.</li>
      </ul>
      <p class="mt-3">
        The longer your horizon, the more risk you may usually accept in exchange for growth potential.
      </p>
      <div class="mt-2">
        <div class="mt-3">
          <YouTubeThumbnail video-id="UTSsOaRJQs4" title="How to choose your investment horizon?"
            href="https://www.youtube.com/watch?v=UTSsOaRJQs4" />
        </div>
      </div>
    </div>

  </div>
</template>
