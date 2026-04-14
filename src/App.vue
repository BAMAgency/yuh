<script setup>
import { onMounted, ref } from 'vue';
import NavBar from './components/core/NavBar.vue'
import TheCalculator from './components/TheCalculator.vue'

const isStarted = ref(false);

onMounted(() => {
  document.title = "Yuh Budget Calculator";
})

</script>



<template>
  <div class="relative min-h-screen overflow-hidden">
    <img
      class="pointer-events-none absolute inset-0 -z-10 h-full w-full object-cover opacity-100"
      src="/assets/Background_Abstract_03.svg"
      alt="Background image"
    />

    <div class="relative z-10 flex flex-col items-center gap-4 text-yuh-black font-display">
    <NavBar />
    <div class="flex flex-col items-center gap-4 max-w-2xl mt-12">
      <div class=" md:text-5xl lg:text-6xl text-3xl mb-6">
        Should yuh invest ?
      </div>
      <div>
        <div v-if="!isStarted" class="flex flex-col text-center items-center gap-4 md:text-xl lg:text-2xl text-lg pr-12 pl-12">
          Find out in 5 minutes with our calculator. <br />
          Discover your budget, risk tolerance and investment horizon to get personalized recommendations.
        </div>
      </div>
      <div v-if="!isStarted" class="mt-12">
        <button @click="isStarted = true">
          <div class="flex flex-row items-center gap-2 bg-yuh-orange text-white rounded-full pl-6 pr-6 p-4 hover:bg-yuh-black hover:cursor-pointer">
            Start the calculator
          </div>
        </button>
      </div>
      <div v-if="isStarted">
        <TheCalculator />
      </div>
    </div>

    </div>
  </div>
</template>
