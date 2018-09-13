<template>
  <div>
    <div class="flex justfy-between flex-col sm:flex-row mb-1">
      <div>
        <span class="mr-3 font-bold">Conway's Game of Life</span>
      </div>
      <div class="flex">
        <span class="mr-3 text-grey-dark text-sm">Ticks: {{ ticks }}</span>
        <span class="mr-3 text-grey-dark text-sm">Cell count: {{ cellsCount }}</span>
        <span class="mr-3 text-grey-dark text-sm">Cells alive: {{ cellsAlive }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Stats',

  props: ['ticks', 'cellsCount', 'cellsAlive']
}
</script>

<style scoped>

</style>
