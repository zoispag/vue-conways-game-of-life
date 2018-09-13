<template>
  <div>
    <div class="fixed pin z-50 overflow-auto flex modal py-8 bg-smoke-dark" v-show="showModal">

      <div class="relative p-16 bg-white w-full max-w-md m-auto flex-col flex">

        <span class="absolute pin-t pin-b pin-r p-4">
          <svg class="fill-current h-10 w-10 text-grey-dark hover:text-grey-darkest" @click="showModal = false" role="button" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><title>Close</title><path d="M14.348 14.849a1.2 1.2 0 0 1-1.697 0L10 11.819l-2.651 3.029a1.2 1.2 0 1 1-1.697-1.697l2.758-3.15-2.759-3.152a1.2 1.2 0 1 1 1.697-1.697L10 8.183l2.651-3.031a1.2 1.2 0 1 1 1.697 1.697l-2.758 3.152 2.758 3.15a1.2 1.2 0 0 1 0 1.698z"/></svg>
        </span>

        <div class="text-sm">

          <h2>Conway's Game of Life</h2>

          <p class="my-3">
            The Game of Life is a cellular automaton devised by the British mathematician <strong>John Horton Conway</strong> in 1970.
          </p>

          <p class="my-3">
            The universe of the Game of Life is an infinite, two-dimensional orthogonal grid of square cells, each of which is in one of two possible states, alive or dead. Every cell interacts with its eight neighbours, which are the cells that are horizontally, vertically, or diagonally adjacent. At each step in time, the following transitions occur:
          </p>

          <p>- Any live cell with fewer than two live neighbors dies, as if by under population.</p>
          <p>- Any live cell with two or three live neighbors lives on to the next generation.</p>
          <p>- Any live cell with more than three live neighbors dies, as if by overpopulation.</p>
          <p>- Any dead cell with exactly three live neighbors becomes a live cell, as if by reproduction.</p>

          <p class="my-3">
            The initial pattern constitutes the seed of the system. The first generation is created by applying the above rules simultaneously to every cell in the seed; births and deaths occur simultaneously, and the discrete moment at which this happens is sometimes called a tick. Each generation is a pure function of the preceding one. The rules continue to be applied repeatedly to create further generations.
          </p>
        </div>
      </div>

    </div>

    <div class="cursor-pointer">
      <svg @click="showModal = true" class="w-4 h-4 fill-current text-red-dark" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
        <path class="heroicon-ui" d="M12 22a10 10 0 1 1 0-20 10 10 0 0 1 0 20zm0-2a8 8 0 1 0 0-16 8 8 0 0 0 0 16zm0-9a1 1 0 0 1 1 1v4a1 1 0 0 1-2 0v-4a1 1 0 0 1 1-1zm0-4a1 1 0 1 1 0 2 1 1 0 0 1 0-2z"/>
      </svg>
    </div>
  </div>
</template>

<script>
export default {
  name: 'InfoModal',

  props: ['openmodal'],

  data () {
    return {
      showModal: this.openmodal
    }
  }
}
</script>

<style scoped>

</style>
