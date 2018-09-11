<template>
  <div>
    <div class="flex justfy-between">
      <span class="mr-3">Cycles: {{ cycles }}</span>
      <span class="mr-3">Cell count: {{ cellsCount }}</span>
      <span class="mr-3">Cells alive: {{ cellsAlive }}</span>
    </div>
    <div id="ConwayBoard" class="flex">
      <div v-for="(row, x) in boardStatus" :key="x">
        <div v-for="(column, y) in row" :key="y">
          <SingleCell :key="`${x}:${y}}`" :isCellAlive="alive(x, y)"></SingleCell>
        </div>
      </div>
    </div>
    <button type="button" class="mt-2 bg-blue hover:bg-blue-dark text-white py-2 px-4 rounded"
      @click="nextConwayCycle()" >Next Cycle</button>
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
      size: 50,
      cycles: 0,
      cellsAlive: 0,
      boardStatus: []
    }
  },

  created () {
    for (var x = 0; x < this.size; x++) {
      this.boardStatus[x] = []
      for (var y = 0; y < this.size; y++) {
        this.boardStatus[x][y] = Math.round(Math.random())
      }
    }
    this.cellsAlive = this.boardStatus.reduce((count, row) => count + row.filter(c => c).length, 0)
  },

  computed: {
    cellsCount: function () {
      return this.size * this.size
    }
  },

  methods: {
    alive: function (x, y) {
      return this.boardStatus[x][y]
    },

    nextConwayCycle: function () {
      this.cycles++
      let grid = []
      for (var x = 0; x < this.size; x++) {
        grid[x] = []
        for (var y = 0; y < this.size; y++) {
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

      // set new gridList content
      for (let x = 0; x < this.size; x++) {
        for (let y = 0; y < this.size; y++) {
          this.boardStatus[x][y] = grid[x][y]
        }
      }
      this.cellsAlive = this.boardStatus.reduce((count, row) => count + row.filter(c => c).length, 0)
    },

    getAliveNeighbours: function (x, y) {
      let neighbours = 0
      if (x <= this.size && y <= this.size) {
        for (let offsetX = -1; offsetX < 2; offsetX++) {
          for (let offsetY = -1; offsetY < 2; offsetY++) {
            let newX = x + offsetX
            let newY = y + offsetY
            // check if offset is: on current cell, out of bounds and if isAlive
            if (
              (offsetX !== 0 || offsetY !== 0) &&
              newX >= 0 &&
              newX < this.size &&
              newY >= 0 &&
              newY < this.size &&
              this.boardStatus[x + offsetX][y + offsetY]
            ) {
              neighbours++
            }
          }
        }
      }
      return neighbours
    }
  }
}
</script>

<style scoped>

</style>
