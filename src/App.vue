<template>
  <div id="app" class="m-6">
    <ConwayBoard />
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
