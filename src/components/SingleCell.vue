<template>
  <div id="SingleCell">
    <div
      class="w-3 h-3 bg-grey-light border border-collapse border-solid border-grey hover:bg-red-lighter"
      :class="{ alive: isCellAlive }"
      @click="$emit('handleClick', [x, y])"
      :title="`x: ${x}, y: ${y}`"
    ></div>
  </div>
</template>

<script>
export default {
  name: 'SingleCell',
  props: ['x', 'y', 'isCellAlive']
}
</script>

<style scoped>
  .alive {
    @apply bg-red-light;
  }
</style>
