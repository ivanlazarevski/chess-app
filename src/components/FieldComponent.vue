<script setup>
const props = defineProps(['isWhite', 'fieldName', 'possibleMove'])
</script>
<template>
  <div :class="props.isWhite ? 'bg-slate-100' : 'bg-slate-800'" class="w-full h-full field cursor-pointer relative flex items-center justify-center">
    <div absolute class="text-pink-600 p-1 font-bold text-xs md:text-base top-2 left-2">{{ fieldName }}</div>
    <div v-if="props.possibleMove" class="possible-move w-8 h-8 bg-blue-500 opacity-50 rounded-full absolute z-10"></div>
  </div>
</template>
