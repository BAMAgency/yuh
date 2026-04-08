<script setup>
import { ref } from 'vue'
import RangeSlider from './RangeSlider.vue'
import YuhSelect from './YuhSelect.vue'
import LearnMoreObjective from './LearnMoreObjective.vue'
import HorizonSelector from './HorizonSelector.vue'
import RiskLevelSelector from './RiskLevelSelector.vue'

const index = ref(0);
const ageInput = ref(25);
const incomeInput = ref(5000);
const currentInvestment = ref(10000);
const debt = ref(5000);
const objective = ref('Invest');
const horizon = ref('medium')
const riskLevel = ref('balanced')
const MIN_AGE = 0
const MAX_AGE = 100
const MIN_INCOME = 0
const MAX_INCOME = 20000
const LAST_INDEX = 3

const objectiveOptions = [

  'Save money',
  'Invest',
  'Prepare for a major purchase',
  'Plan for retirement'
]

function goPrevious() {
  if (index.value > 0) {
    index.value -= 1
  }
}

function goNext() {
  if (index.value < LAST_INDEX) {
    index.value += 1
  }
}

</script>

<template>
  <div class="flex flex-col items-center gap-12 mb-12">

    <!-- Premières questions d'onboarding -->
    <div v-if="index == 0"
      class="rounded-xl bg-white lg:min-w-xl md:min-w-md sm:min-w-sm sm:rounded-xl p-12 shadow-xl text-center text-sm md:text-base lg:text-lg">
      <form action="" class="flex flex-col gap-8">
        <RangeSlider v-model="ageInput" id="age" name="age" label="Age" :min="MIN_AGE" :max="MAX_AGE" />
        <RangeSlider v-model="incomeInput" id="income" name="income" label="Monthly income" :min="MIN_INCOME"
          :max="MAX_INCOME" step="100" info="In CHF, how much do you percieve monthly?" />
        <RangeSlider v-model="currentInvestment" id="current-investment" name="current-investment"
          label="Current investment" :min="0" :max="100000" step="100"
          info="In CHF, how much have you already invested?" />
        <RangeSlider v-model="debt" id="debt" name="debt" label="Debt" :min="0" :max="10000" step="100" />
      </form>
    </div>

    <!-- Deuxièmes questions : objectifs principaux -->
    <div v-if="index == 1"
      class="rounded-xl bg-white lg:min-w-xl md:min-w-md sm:min-w-sm sm:rounded-xl p-12 shadow-xl text-center text-sm md:text-base lg:text-lg">
      <form action="" class="flex flex-col gap-8">
        <YuhSelect
          v-model="objective"
          id="objective"
          name="objective"
          label="What is your main objective?"
          info="Select the goal that best matches your current financial priority."
          :options="objectiveOptions"
        />
        <LearnMoreObjective />
      </form>
    </div>


    <!-- Troisèmes questions : horizon -->
    <div v-if="index == 2"
      class="rounded-xl bg-white lg:min-w-xl md:min-w-md sm:min-w-sm sm:rounded-xl p-12 shadow-xl text-center text-sm md:text-base lg:text-lg">
       <form action="" class="flex flex-col gap-8">
         <HorizonSelector v-model="horizon" />
       </form>
    </div>

    <!-- Quatrièmes questions : risk level -->
    <div v-if="index == 3"
      class="rounded-xl bg-white lg:min-w-xl md:min-w-md sm:min-w-sm sm:rounded-xl p-12 shadow-xl text-center text-sm md:text-base lg:text-lg">
       <form action="" class="flex flex-col gap-8">
         <RiskLevelSelector v-model="riskLevel" />
       </form>
    </div>

    <!-- Navigation buttons -->
    <div class="flex flex-row gap-12">
      <button v-if="index > 0" type="button" @click="goPrevious" :disabled="index === 0">
        <div
          class="flex flex-row items-center gap-2 pl-4 pr-4 p-4 text-yuh-orange hover:text-yuh-black hover:cursor-pointer">
          < Previous </div>
      </button>
      <button v-if="index < LAST_INDEX" type="button" @click="goNext" :disabled="index >= LAST_INDEX">
        <div
          class="flex flex-row items-center gap-2 text-yuh-orange hover:text-yuh-black rounded-full pl-4 pr-4 p-4  hover:cursor-pointer">
          Next >
        </div>
      </button>
    </div>

  </div>
</template>
