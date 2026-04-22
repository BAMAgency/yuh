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
            <span class="font-bold">How can I start investing?   </span>
            <span class="ml-auto text-sm font-bold text-yuh-orange">YuhLearn {{ isOpen ? 'x' : '+' }}</span>
            </div>
        </button>

        <div v-if="isOpen" id="risk-level-help-panel" class="px-6 pb-5 text-left text-sm font-medium text-yuh-black flex flex-col">
            <p class="mb-2">
                The example alllocation are based on the information you gave us. 
                The pourcentages are directly based on the risk level you chose, and the horizon you have. 
            </p>
            <ul class="list-disc space-y-1 pl-5 font-bold">
                With Yuh, you can start learning about investing today !
            </ul>
            <p class="mt-3">
                With YuhLearn, you can explore content tailored to learn investment easily, and at your own pace. Whether you’re a beginner or looking to deepen your knowledge, we’ve got you covered with articles, videos, and interactive tools to help you understand the basics of investing and make informed decisions.
            </p>
            <div class="mt-2">
                <div class="mt-3">
                    <div>
                        <a class="text-yuh-orange hover:underline" href="
                        https://www.yuh.com/fr/yuhlearn/cinq-strategies-pour-les-cinq-types-dinvestissement/"> Click here to learn more!</a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
