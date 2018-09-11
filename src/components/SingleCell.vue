<template>
  <div id="SingleCell">
    <div
      class="w-3 h-3 bg-grey-light border border-collapse border-solid border-grey"
      :class="{ alive: isCellAlive }"
    ></div>
  </div>
</template>

<script>
export default {
  name: 'SingleCell',
  props: ['isCellAlive']
}
</script>

<style scoped>
  .alive {
    @apply bg-red;
  }
</style>
