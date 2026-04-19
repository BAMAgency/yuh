<script setup>
import { ref } from 'vue'
import { ChevronDown } from 'lucide-vue-next'

const logoPath = `${import.meta.env.BASE_URL}assets/Yuh_Logo_Single_Black_RGB.svg`

const isMenuOpen = ref(false)

function toggleMenu() {
  isMenuOpen.value = !isMenuOpen.value
}

function closeMenu() {
  isMenuOpen.value = false
}
</script>

<template>
  <nav
    class="bg-gray-100 m-4 mr-4 ml-4 relative flex w-full max-w-sm md:max-w-md lg:max-w-6xl flex-row items-center gap-4 p-4 shadow-lg rounded-full"
  >
    <div>
      <img class="h-10 w-auto" :src="logoPath" alt="Yuh's logo" />
    </div>

    <div id="nav-links" class="hidden lg:absolute lg:left-1/2 lg:block lg:-translate-x-1/2">
      <ol class="flex flex-row gap-12">
        <li class="text-yuh-orange hover:cursor-pointer hover:bg-white hover:rounded-full p-2">
          App
        </li>
        <li class="hover:cursor-pointer hover:bg-white hover:rounded-full p-2">
          Pricing
        </li>
        <li class="hover:cursor-pointer hover:bg-white hover:rounded-full p-2">
          YuhLearn
        </li>
        <li class="hover:cursor-pointer hover:bg-white hover:rounded-full p-2">
          About Yuh
        </li>
        <li class="hover:cursor-pointer hover:bg-white hover:rounded-full p-2">
          Help
        </li>
      </ol>
    </div>

    <div class="ml-auto flex items-center gap-2">
      <div id="ctas" class="flex flex-row gap-2">
        <label for="language-select" class="sr-only">Language</label>
        <div class="relative inline-flex items-center">
          <select
            id="language-select"
            name="language"
            class="appearance-none rounded-full bg-gray-200 px-3 py-1 pr-8"
          >
            <option value="en">English</option>
            <option value="fr">Francais</option>
            <option value="de">Deutsch</option>
          </select>
          <span class="pointer-events-none absolute right-3 text-yuh-orange">
            <ChevronDown class="h-4 w-4" />
          </span>
        </div>
        <button class="hover:cursor-pointer bg-yuh-orange text-white rounded-full pl-6 pr-6 p-4 hidden lg:block">
          Get started
        </button>
      </div>

      <button
        class="rounded-full hover:cursor-pointer bg-white px-3 py-2 lg:hidden"
        type="button"
        aria-controls="nav-links"
        :aria-expanded="isMenuOpen"
        aria-label="Toggle navigation"
        @click="toggleMenu"
      >
        <span v-if="!isMenuOpen">=</span>
        <span v-else>X</span>
      </button>
    </div>

    <div v-if="isMenuOpen" class="absolute left-4 right-4 top-full z-10 mt-2 rounded bg-white p-4 shadow lg:hidden">
      <ol class="flex flex-col gap-3 hover:cursor-pointer">
        <li class="text-yuh-orange" @click="closeMenu">
          App
        </li>
        <li @click="closeMenu">
          Pricing
        </li>
        <li @click="closeMenu">
          YuhLearn
        </li>
        <li @click="closeMenu">
          About Yuh
        </li>
        <li @click="closeMenu">
          Help
        </li>
      </ol>
    </div>
  </nav>
</template>

<style scoped></style>
