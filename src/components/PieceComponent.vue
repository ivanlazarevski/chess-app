<script setup>
const props = defineProps(['xPos', 'yPos', 'image', 'selected'])
</script>

<template>
  <div
    class="absolute piece w-1/8 h-1/8 flex items-center justify-center text-6xl cursor-pointer bg-center bg-contain"
    :class="selected ? 'selected' : ''"
    :style="{
      transform: `translate(${Number(props.xPos) * 100}%, ${Number(props.yPos) * 100}%)`,
      backgroundImage: `url(${props.image})`,
    }"
  ></div>
</template>

<style scoped>
.selected {
  background-color: #2980b9;
}
</style>
