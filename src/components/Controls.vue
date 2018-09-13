<template>
  <div>
    <div class="flex justfy-between flex-col sm:flex-row">
      <InfoModal></InfoModal>
      <button
        type="button"
        class="mt-2 sm:ml-2 bg-blue hover:bg-blue-dark text-white py-2 px-4 text-sm rounded"
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

    <div class="text-xs flex flex-col sm:flex-row w-full mt-2 sm:mt-3">
      <div class="flex justify-center bg-grey-dark text-white p-3 pt-4 rounded">
        <span class="font-bold tracking-wide">Width</span>
        <div class="mx-2 relative" style="bottom:0.1rem">
          <input type="range" min="10" max="100" step="10" v-model="width" @click="$emit('toggleResetCells')" />
        </div>
        <span>{{ width }}</span>
        <span
          @click="$emit('toggleResetWidth')"
          class="ml-2 hover:text-red-dark cursor-pointer text-sm relative"
          style="bottom:0.2rem"
        >&#8634;</span>
      </div>
      <div class="flex justify-center bg-grey-dark text-white p-3 pt-4 rounded sm:ml-2 mt-2 sm:mt-0">
        <span class="font-bold tracking-wide">Height</span>
        <div class="mx-2 relative" style="bottom:0.1rem">
          <input type="range" min="10" max="100" step="10" v-model="height" @click="$emit('toggleResetCells')" />
        </div>
        <span>{{ height }}</span>
        <span
          @click="$emit('toggleResetHeight')"
          class="ml-2 hover:text-red-dark cursor-pointer text-sm relative"
          style="bottom:0.2rem"
        >&#8634;</span>
      </div>
    </div>
  </div>
</template>

<script>
import InfoModal from '@/components/InfoModal.vue'

export default {
  name: 'Controls',

  components: {
    InfoModal
  },

  props: ['interval', 'speeds', 'speedId', 'width', 'height', 'activeResetButton'],

  computed: {
    btnText () {
      return this.interval !== undefined ? 'Stop' : 'Start'
    }
  }
}
</script>

<style scoped>

  input[type=range] {
    -webkit-appearance: none;
    margin: 2.75px 0;
    @apply bg-grey-dark;
  }

  input[type=range]:focus {
    outline: none;
  }

  input[type=range]::-webkit-slider-runnable-track {
    height: 5px;
    @apply bg-white rounded cursor-pointer w-full;
  }

  input[type=range]::-webkit-slider-thumb {
    box-shadow: 0px 0px 1px #670000, 0px 0px 0px #810000;
    border: 0px solid #ff1e00;
    height: 12px;
    width: 12px;
    border-radius: 50px;
    -webkit-appearance: none;
    margin-top: -3px;
    @apply bg-red-light cursor-pointer;
  }

  input[type=range]:hover::-webkit-slider-thumb {
    @apply bg-red-lighter;
  }

  input[type=range]::-moz-range-track {
    height: 5px;
    @apply bg-white rounded cursor-pointer w-full;
  }

  input[type=range]::-moz-range-thumb {
    box-shadow: 0px 0px 1px #670000, 0px 0px 0px #810000;
    border: 0px solid #ff1e00;
    height: 12px;
    width: 12px;
    border-radius: 50px;
    -webkit-appearance: none;
    margin-top: -3px;
    @apply bg-red-light cursor-pointer;
  }

</style>
