<template>
  <div>
    <div class="flex justfy-between flex-col sm:flex-row mb-1">
      <div class="flex mr-3">
        <span class="font-bold mr-1">Conway's Game of Life</span>
        <InfoModal></InfoModal>
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
import InfoModal from '@/components/InfoModal.vue'

export default {
  name: 'Stats',

  components: {
    InfoModal
  },

  props: ['ticks', 'cellsCount', 'cellsAlive']
}
</script>

<style scoped>

</style>
