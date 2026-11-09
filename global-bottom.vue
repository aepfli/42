<script setup>
import { computed } from 'vue'
import { useNav } from '@slidev/client'

const { currentSlideNo, total, currentSlideRoute } = useNav()

// Hide the chrome on cover/end slides where it would be visually noisy.
const hideOn = new Set(['cover', 'intro', 'end'])
const hidden = computed(() => hideOn.has(currentSlideRoute.value?.meta?.layout))

// Progress toward the end of the deck as a fraction [0, 1].
const progress = computed(() => {
  const t = total.value
  if (!t || t < 2) return 0
  return Math.max(0, Math.min(1, (currentSlideNo.value - 1) / (t - 1)))
})
</script>

<template>
  <div v-if="!hidden" class="fixed bottom-0 left-0 right-0 z-10 pointer-events-none select-none">
    <div class="h-[3px] w-full bg-gray-200/50">
      <div
        class="h-full bg-gray-700 dark:bg-gray-200 transition-all duration-500 ease-out"
        :style="{ width: `${progress * 100}%` }"
      ></div>
    </div>
    <div class="absolute right-3 bottom-2 text-[10px] font-mono opacity-40 leading-none tabular-nums">
      {{ currentSlideNo }} / {{ total }}
    </div>
  </div>
</template>
