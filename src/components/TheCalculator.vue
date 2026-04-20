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
import { ChevronLeft } from 'lucide-vue-next'
import { ChevronRight } from 'lucide-vue-next'

const investPath = `${import.meta.env.BASE_URL}assets/invest.png`


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
    <div v-if="index == 0" class="lg:min-w-xl md:min-w-md sm:min-w-sm text-center text-sm md:text-base lg:text-lg">

      <div class="mb-6 flex flex-col gap-2">
        <h1 class="text-2xl font-bold">Let's get started!</h1>
        <p class="font-medium text-md">Answer a few questions about yourself to get personalized financial advice.</p>
      </div>

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
          <ChevronLeft /> Previous
        </div>
      </button>
      <button v-if="index < LAST_INDEX" type="button" @click="goNext" :disabled="index >= LAST_INDEX">
        <div
          class="flex flex-row items-center gap-2 bg-yuh-orange text-white hover:bg-yuh-orange/90 rounded-4xl pl-4 pr-4 p-4  hover:cursor-pointer">
          Next
          <ChevronRight />
        </div>
      </button>
    </div>

    <!--Disclaimer-->
    <div class="text-xs text-yuh-black/70 mt-4">
      <p>Disclaimer: This calculator provides general financial guidance based on the information you provide. It is not
        a substitute for personalized advice from a qualified financial advisor. The results are estimates and may not
        reflect actual outcomes. Always consider your individual circumstances and consult with a professional before
        making any financial decisions.</p>
    </div>



  </div>
  <div v-if="index == 5" class="flex flex-col justify-center gap-6 p-4">
    <h1 class="text-6xl">With Yuh, investment is simple.</h1>

    <div>
      <p class="text-2xl text-left">Your financial future is just a few steps away!</p>
      <p class="text-lg font-medium">Yuh is a swiss financial application that makes investing accessible to everyone.
      </p>
    </div>

    <div class="grid lg:grid-cols-2 grid-cols-1 items-center">
      <img :src="investPath" alt="Why Yuh?" class="h-full lg:-translate-x-1/4" />
      <div class="align-left flex flex-col gap-6">
        <div class="flex flex-col gap-4">
          <h2 class="text-4xl text-yuh-purple text-left">Why choose Yuh?</h2>
          <ul class="list-disc font-medium text-left text-base flex flex-col gap-4">
            <li>Easy access to investment products</li>
            <li>0 trading fee ETFs</li>
            <li>Trade in fractions</li>
            <li>Recurring auto-investments</li>
          </ul>
        </div>
        <div class="mb-6">
          <h3 class="text-2xl font-bold text-yuh-rose mb-4">And so much more</h3>
          <p class="text-base font-medium text-left">Yuh also offers a wide range of features to help you manage your
            finances
            and reach your goals, including saving tools, secure online payment processing, and more.</p>
        </div>
      </div>
    </div>

    <div class="flex flex-col gap-6 mb-34 items-center">
      <h2 class="text-4xl">Try it for free now !</h2>
      <p class="text-lg font-medium text-left">Join thousands of satisfied users and start your investment journey with
        Yuh today</p>
      <button
        class="bg-yuh-orange w-min text-white rounded-full pl-12 pr-12 p-2 hover:bg-yuh-orange/90 hover:cursor-pointer">
        Get started
      </button>
    </div>

  </div>

  <div class="text-sm text-yuh-black mt-4 mb-4 text-center">
    Academic project HEIG-VD x Yuh.
    This is a student prototype. Recommendations are
    theoretical and do not constitute financial advice.
    Visit www.yuh.com for official services.
</div>
</template>
