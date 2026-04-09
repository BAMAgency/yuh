<script setup>
import { Play } from 'lucide-vue-next'

defineProps({
  videoId: {
    type: String,
    required: true
  },
  title: {
    type: String,
    required: true
  },
  href: {
    type: String,
    required: true
  }
})

function thumbnailUrl(videoId) {
  return `https://img.youtube.com/vi/${videoId}/hqdefault.jpg`
}
</script>

<template>
  <h2 class="font-bold mt-3 mb-3 text-center text-md text-yuh-purple">Watch that video guide to learn more!</h2>
  <a
    :href="href"
    target="_blank"
    rel="noreferrer"
    class="group block overflow-hidden rounded-2xl border border-yuh-pale-violet bg-white shadow-sm transition-transform duration-200 hover:-translate-y-0.5 hover:shadow-lg"
    :aria-label="`Open YouTube video: ${title}`"
  >
    <div class="relative aspect-video w-full overflow-hidden bg-yuh-pale-violet">
      <img
        class="h-full w-full object-cover transition-transform duration-300 group-hover:scale-105"
        :src="thumbnailUrl(videoId)"
        :alt="title"
      />

      <div class="absolute inset-0 bg-linear-to-t from-yuh-black/60 via-yuh-black/10 to-transparent"></div>

      <div class="absolute inset-0 flex items-center justify-center">
        <div class="flex h-16 w-16 items-center justify-center rounded-full bg-white/95 text-yuh-orange shadow-lg transition-transform duration-200 group-hover:scale-110">
          <Play class="ml-1 h-6 w-6" />
        </div>
      </div>
    </div>

    <div class="flex items-center justify-between gap-3 px-4 py-3 text-left">
      <div>
        <p class="text-sm font-semibold text-yuh-black">Watch the guide</p>
        <p class="text-xs text-yuh-black/60">Short video tutorial on YouTube</p>
      </div>
      <span class="rounded-full bg-yuh-pale-violet px-3 py-1 text-xs font-semibold text-yuh-black">
        1 min
      </span>
    </div>
  </a>
</template>
