<script setup>
import { ref, shallowRef } from 'vue'
import RangeSlider from './core/RangeSlider.vue'
import YuhSelect from './YuhSelect.vue'
import LearnMoreObjective from './learnmoreSections/LearnMoreObjective.vue'
import HorizonSelector from './HorizonSelector.vue'
import LearnMoreHorizon from './learnmoreSections/LearnMoreHorizon.vue'
import RiskLevelSelector from './RiskLevelSelector.vue'
import LearnMoreRiskLevel from './learnmoreSections/LearnMoreRiskLevel.vue'
import TheExpenseSection from './TheExpenseSection.vue'
import TheDashboard from './TheDashboard.vue'
import InfoTooltipButton from './core/InfoTooltipButton.vue'

const index = ref(0);
const ageInput = ref(25);
const incomeInput = ref(5000);
const currentInvestment = ref(1000);
const debt = ref(500);
const save = ref(10000);
const objective = ref('Invest');
const horizon = ref('medium')
const riskLevel = ref('Mild')
const expenses = shallowRef([])
const highInterestDebt = ref(false);
const LAST_INDEX = 5

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
  console.log('expenses', expenses.value)
}

function goNext() {
  if (index.value < LAST_INDEX) {
    index.value += 1
  }
  console.log('expenses', expenses.value)
}

</script>

<template>
  <div class="flex flex-col items-center gap-12 mb-12 rounded-xl bg-white shadow-xl p-12">

    <!-- Première section : l'onboarding -->
    <div v-if="index == 0"
      class="lg:min-w-xl md:min-w-md sm:min-w-sm text-center text-sm md:text-base lg:text-lg">
      <form action="" class="flex flex-col gap-8">

        <RangeSlider v-model="ageInput" id="age" name="age" label="Age" :min="18" :max="75" step="1" />

        <RangeSlider v-model="incomeInput" id="income" name="income" label="Monthly income" :min="0" :max="30000"
          step="100" info="Gross monthly income in CHF" />

        <RangeSlider v-model="save" id="save" name="save" label="Total savings" :min="0" :max="500000" step="1000"
          info="How much cash savings do you currently have in CHF?" />

        <RangeSlider v-model="currentInvestment" id="current-investment" name="current-investment"
          label="Total investment" :min="0" :max="100000" step="50" info="Total value of your invested assets in CHF" />

        <RangeSlider v-model="debt" id="debt" name="debt" label="Total debt" :min="0" :max="1000000" step="1000"
          info="Total outstanding debt in CHF" />

        <label for="high-interest-debt"
          class="flex items-start gap-3 text-left text-sm text-yuh-black/70 hover:cursor-pointer">
          <input id="high-interest-debt" name="high-interest-debt" type="checkbox" v-model="highInterestDebt"
            class="mt-1 h-5 w-5 rounded border-yuh-purple text-yuh-orange focus:ring-yuh-orange" />
          <span>I have high interest debt (e.g. credit card debt)</span>
          <InfoTooltipButton
            text="High interest debt can significantly impact your financial health. If you have high interest debt, it's often advisable to prioritize paying it off before investing, as the interest on such debt can outweigh potential investment returns.">
          </InfoTooltipButton>
        </label>

      </form>
    </div>

    <!-- Deuxième section : objectifs principaux -->
    <div v-if="index == 1"
      class=" bg-white lg:min-w-xl md:min-w-md sm:min-w-sm  text-center text-sm md:text-base lg:text-lg">
      <form action="" class="flex flex-col gap-8">
        <YuhSelect v-model="objective" id="objective" name="objective" label="What is your main objective?"
          info="Select the goal that best matches your current financial priority." :options="objectiveOptions" />
        <LearnMoreObjective />
      </form>
    </div>


    <!-- Troisème section : horizon -->
    <div v-if="index == 2"
      class=" bg-white lg:min-w-xl md:min-w-md sm:min-w-sm text-center text-sm md:text-base lg:text-lg">
      <form action="" class="flex flex-col gap-8">
        <HorizonSelector v-model="horizon" />
        <LearnMoreHorizon />
      </form>
    </div>

    <!-- Quatrième section : risk level -->
    <div v-if="index == 3"
      class=" bg-white lg:min-w-xl md:min-w-md sm:min-w-sm text-center text-sm md:text-base lg:text-lg">
      <form action="" class="flex flex-col gap-8">
        <RiskLevelSelector v-model="riskLevel" />
        <LearnMoreRiskLevel />
      </form>
    </div>

    <!--Cinquième section : les dépenses -->
    <div v-if="index == 4"
      class=" bg-white lg:min-w-xl md:min-w-md sm:min-w-sm  text-center text-sm md:text-base lg:text-lg">
      <form>
        <TheExpenseSection v-model="expenses" />
      </form>
    </div>

    <!-- Sixième section : le résultat -->
    <div v-if="index == 5"
      class=" bg-white lg:min-w-xl md:min-w-md sm:min-w-sm text-center text-sm md:text-base lg:text-lg p-2">
      <TheDashboard :age="ageInput" :income="incomeInput" :currentInvestment="currentInvestment" :debt="debt"
        :objective="objective" :horizon="horizon" :riskLevel="riskLevel" :expenses="expenses" :save="save"
        :highInterestDebt="highInterestDebt" />
    </div>

    <!-- Navigation buttons -->
    <div class="flex flex-row gap-12">
      <button v-if="index > 0" type="button" @click="goPrevious" :disabled="index === 0">
        <div
          class="flex flex-row items-center gap-2 pl-4 pr-4 p-4 bg-yuh-orange text-white rounded-4xl hover:bg-yuh-orange/90 hover:cursor-pointer">
          <  Previous </div>
      </button>
      <button v-if="index < LAST_INDEX" type="button" @click="goNext" :disabled="index >= LAST_INDEX">
        <div
          class="flex flex-row items-center gap-2 bg-yuh-orange text-white hover:bg-yuh-orange/90 rounded-4xl pl-4 pr-4 p-4  hover:cursor-pointer">
          Next  >
        </div>
      </button>
    </div>

  </div>
</template>
