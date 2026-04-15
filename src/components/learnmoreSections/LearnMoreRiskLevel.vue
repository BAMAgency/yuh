<script setup>
import { ref } from 'vue'


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
            <div class="flex w-full items-center gap-2">
            <span class="font-bold">How to choose your risk level?</span>
            <span class="ml-auto text-sm font-bold text-yuh-orange">YuhLearn {{ isOpen ? 'x' : '+' }}</span>
            </div>
        </button>

        <div v-if="isOpen" id="risk-level-help-panel" class="px-6 pb-5 text-left text-sm font-medium text-yuh-black flex flex-col">
            <p class="mb-2">
                Your risk level reflects how much value fluctuation you are comfortable with in exchange for possible
                returns.
            </p>
            <ul class="list-disc space-y-1 pl-5">
                <li><span class="font-bold">Mild:</span> lower risk, steadier performance, and more capital
                    protection.</li>
                <li><span class="font-bold">Tasty:</span> a mix of growth and stability.</li>
                <li><span class="font-bold">Spicy:</span> higher risk, more ups and downs, and higher growth
                    potential.</li>
                <li><span class="font-bold">Hot:</span> Tabasco alert: 80% of your assets are invested. Risky but suitable for the long term.</li>
                <li><span class="font-bold">Extra Hot:</span> with 99% of your assets invested in stocks, an intense experience awaits you.</li>
            </ul>
            <p class="mt-3">
                Pick the level that matches your comfort with market changes and your investment horizon.
            </p>
            <div class="mt-2">
                <div class="mt-3">
                    <div>
                        <a class="text-yuh-orange hover:underline" href="
                        https://www.yuh.com/fr/yuhlearn/cinq-strategies-pour-les-cinq-types-dinvestissement/"> Read that article to learn more !</a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
