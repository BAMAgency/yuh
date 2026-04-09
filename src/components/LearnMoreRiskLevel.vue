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
        <button type="button"
            class="flex w-full items-center justify-between px-6 py-4 text-yuh-black hover:cursor-pointer"
            :aria-expanded="isOpen" aria-controls="risk-level-help-panel" @click="toggle">
            <span class="font-bold">How to choose your risk level?</span>
            <span class="text-sm font-bold">{{ isOpen ? '^' : 'v' }}</span>
        </button>

        <div v-if="isOpen" id="risk-level-help-panel" class="px-6 pb-5 text-left text-sm font-medium text-yuh-black">
            <p class="mb-2">
                Your risk level reflects how much value fluctuation you are comfortable with in exchange for possible
                returns.
            </p>
            <ul class="list-disc space-y-1 pl-5">
                <li><span class="font-bold">Prudent:</span> lower risk, steadier performance, and more capital
                    protection.</li>
                <li><span class="font-bold">Balanced:</span> a mix of growth and stability.</li>
                <li><span class="font-bold">Dynamic:</span> higher risk, more ups and downs, and higher growth
                    potential.</li>
            </ul>
            <p class="mt-3">
                Pick the level that matches your comfort with market changes and your investment horizon.
            </p>
            <div class="mt-2">
                <div class="mt-3">
                    <YouTubeThumbnail video-id="So9ukzf4sKI" title="How to choose your investment horizon?"
                        href="https://youtu.be/So9ukzf4sKI?si=MS9V_NZB5eT6CgMh" />
                </div>
            </div>
        </div>
    </div>
</template>
