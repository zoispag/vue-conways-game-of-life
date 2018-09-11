<template>
  <div>
    <div class="flex justfy-between mb-1">
      <span class="mr-3 font-bold">Conway's Game of Life</span>
      <span class="mr-3 text-grey-dark text-sm">Cycles: {{ cycles }}</span>
      <span class="mr-3 text-grey-dark text-sm">Cell count: {{ cellsCount }}</span>
      <span class="mr-3 text-grey-dark text-sm">Cells alive: {{ cellsAlive }}</span>
    </div>
    <div id="ConwayBoard" class="flex">
      <div v-for="(row, x) in boardStatus" :key="x">
        <div v-for="(column, y) in row" :key="y">
          <SingleCell
            :key="`${x}:${y}}`"
            :x="x" :y="y"
            :isCellAlive="alive(x, y)"
            @handleClick="toggleSingleCell($event)"
          ></SingleCell>
        </div>
      </div>
    </div>
    <button
      type="button"
      class="mt-2 bg-blue hover:bg-blue-dark text-white py-2 px-4 text-sm rounded"
      @click="nextConwayCycle()"
    >Next cycle</button>
    <button
      type="button"
      class="mt-2 ml-2 bg-green hover:bg-green-dark text-white py-2 px-4 text-sm rounded"
      @click="toggleRun()"
    >{{ btnText }}</button>
    <button
      type="button"
      class="mt-2 ml-2 bg-grey hover:bg-grey-dark text-white py-2 px-4 text-sm rounded"
      @click="toggleSpeed()"
    >{{ speeds[speedId].display }}</button>
    <button
      type="button"
      class="mt-2 ml-2 bg-red hover:bg-red-dark text-white py-2 px-4 text-sm rounded"
      @click="resetCells()"
    >Reset board</button>
  </div>
</template>

<script>
import SingleCell from '@/components/SingleCell.vue'

export default {
  name: 'ConwayBoard',

  components: {
    SingleCell
  },

  data () {
    return {
      width: 50,
      height: 50,
      cycles: 0,
      cellsAlive: 0,
      boardStatus: [],
      interval: undefined,
      speedId: 0,
      speeds: [
        { display: 'x1', milisecs: 1000 },
        { display: 'x2', milisecs: 500 },
        { display: 'x4', milisecs: 250 },
        { display: 'x8', milisecs: 125 },
        { display: 'x10', milisecs: 100 },
        { display: 'x20', milisecs: 50 },
        { display: 'x200', milisecs: 5 }
      ]
    }
  },

  created () {
    this.resetCells()
  },

  computed: {
    cellsCount: function () {
      return this.width * this.height
    },

    btnText: function () {
      return this.interval !== undefined ? 'Stop' : 'Start'
    }
  },

  methods: {
    alive: function (x, y) {
      return this.boardStatus[x][y]
    },

    resetCells: function () {
      for (var x = 0; x < this.width; x++) {
        this.boardStatus[x] = []
        for (var y = 0; y < this.height; y++) {
          this.boardStatus[x][y] = Math.round(Math.random())
        }
      }
      this.cellsAlive = this.boardStatus.reduce((count, row) => count + row.filter(c => c).length, 0)
    },

    toggleRun: function () {
      if (this.interval === undefined) {
        this.startCycle()
      } else {
        this.stopCycle()
      }
    },

    startCycle: function () {
      this.interval = setInterval(this.nextConwayCycle, this.speeds[this.speedId].milisecs)
    },

    stopCycle: function () {
      clearInterval(this.interval)
      this.interval = undefined
    },

    toggleSpeed: function () {
      this.speedId = this.speedId === this.speeds.length - 1 ? this.speedId = 0 : this.speedId + 1
      if (this.interval !== undefined) {
        this.stopCycle()
        this.startCycle()
      }
    },

    nextConwayCycle: function () {
      this.cycles++
      let grid = []
      for (var x = 0; x < this.width; x++) {
        grid[x] = []
        for (var y = 0; y < this.height; y++) {
          let isAlive = this.boardStatus[x][y]
          let aliveNeighbours = this.getAliveNeighbours(x, y)
          let result = 0

          // Rule #1: Any live cell with fewer than two live neighbors dies, as if by under population.
          if (isAlive && aliveNeighbours < 2) {
            result = 0
          }
          // Rule #2: Any live cell with two or three live neighbors lives on to the next generation.
          if ((isAlive && aliveNeighbours === 2) || aliveNeighbours === 3) {
            result = 1
          }
          // Rule #3: Any live cell with more than three live neighbors dies, as if by overpopulation.
          if (isAlive && aliveNeighbours > 3) {
            result = 0
          }
          // Rule #4: Any dead cell with exactly three live neighbors becomes a live cell, as if by reproduction.
          if (!isAlive && aliveNeighbours === 3) {
            result = 1
          }

          grid[x][y] = result
        }
      }

      // set new boardStatus content
      for (let x = 0; x < this.width; x++) {
        for (let y = 0; y < this.height; y++) {
          this.boardStatus[x][y] = grid[x][y]
        }
      }
      this.cellsAlive = this.boardStatus.reduce((count, row) => count + row.filter(c => c).length, 0)
    },

    getAliveNeighbours: function (x, y) {
      let neighbours = 0
      if (x <= this.width && y <= this.height) {
        for (let offsetX = -1; offsetX < 2; offsetX++) {
          for (let offsetY = -1; offsetY < 2; offsetY++) {
            let newX = x + offsetX
            let newY = y + offsetY
            // check if offset is: on current cell, out of bounds and if isAlive
            if (
              (offsetX !== 0 || offsetY !== 0) &&
              newX >= 0 && newX < this.width &&
              newY >= 0 && newY < this.height &&
              this.boardStatus[x + offsetX][y + offsetY]
            ) {
              neighbours++
            }
          }
        }
      }
      return neighbours
    },

    toggleSingleCell: function ([x, y]) {
      this.boardStatus[x][y] = !this.boardStatus[x][y]
      this.cellsAlive = this.boardStatus[x][y] ? this.cellsAlive + 1 : this.cellsAlive - 1
    }
  }
}
</script>

<style scoped>

</style>
