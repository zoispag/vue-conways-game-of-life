<template>
  <div id="app" class="m-6">
    <h1 class="mb-2 text-lg">Conway's Game of Life</h1>
    <div>
      <ConwayBoard />
    </div>
  </div>
</template>

<script>
import ConwayBoard from '@/components/ConwayBoard.vue'

export default {
  name: 'GameOfLife',
  components: {
    ConwayBoard
  }
}
</script>

<style>
@import 'assets/styles/tailwind.postcss';

</style>
