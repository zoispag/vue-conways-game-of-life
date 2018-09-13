<template>
  <div>
    <div class="flex justfy-between flex-col sm:flex-row">
      <button
        type="button"
        class="mt-2 bg-blue hover:bg-blue-dark text-white py-2 px-4 text-sm rounded"
        @click="$emit('toggleNextConwayCycle')"
      >Next tick</button>
      <button
        type="button"
        class="mt-2 sm:ml-2 bg-green hover:bg-green-dark text-white py-2 px-4 text-sm rounded"
        @click="$emit('toggleRun')"
      >{{ btnText }}</button>
      <button
        type="button"
        class="mt-2 sm:ml-2 bg-grey-dark hover:bg-grey-darker text-white py-2 px-4 text-sm rounded"
        @click="$emit('toggleSpeed')"
      >{{ speeds[speedId].display }}</button>
      <button
        type="button"
        class="mt-2 sm:ml-2 bg-red hover:bg-red-dark text-white py-2 px-4 text-sm rounded"
        @click="$emit('toggleResetCells')"
      >Reset board</button>
      <button
        type="button"
        class="mt-2 sm:ml-2 bg-orange hover:bg-orange-dark text-white py-2 px-4 text-sm rounded"
        @click="$emit('toggleClearBoard')"
      >Clear board</button>
      <button
        type="button"
        class="mt-2 sm:ml-2 bg-indigo hover:bg-indigo-dark text-white py-2 px-4 text-sm rounded"
        @click="$emit('toggleResetToStart')"
        :disabled="!activeResetButton"
        :class="{ 'bg-indigo-lighter hover:bg-indigo-lighter cursor-not-allowed' : !activeResetButton }"
      >Reset to Start</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Controls',

  props: ['interval', 'speeds', 'speedId', 'activeResetButton'],

  computed: {
    btnText () {
      return this.interval !== undefined ? 'Stop' : 'Start'
    }
  }
}
</script>

<style scoped>

</style>
