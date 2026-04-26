<template>
  <div class="flex items-center gap-3">
    <button
      @click="$emit('decrease')"
      class="inline-flex items-center justify-center rounded-md h-8 w-8 border border-input bg-background hover:bg-accent hover:text-accent-foreground transition-colors"
    >
      <Minus class="h-4 w-4" />
    </button>
    <span class="text-sm font-medium w-16 text-center">{{ value }} wpm</span>
    <button
      @click="$emit('increase')"
      class="inline-flex items-center justify-center rounded-md h-8 w-8 border border-input bg-background hover:bg-accent hover:text-accent-foreground transition-colors"
    >
      <Plus class="h-4 w-4" />
    </button>
  </div>
</template>

<script setup>
import { Minus, Plus } from 'lucide-vue-next'

defineProps({
  value: Number,
})
defineEmits(['increase', 'decrease'])
</script>
