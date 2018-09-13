<template>
  <div>
    <Stats :ticks="cycles" :cells-count="cellsCount" :cells-alive="cellsAlive"></Stats>

    <div id="ConwayBoard" class="flex">
      <div v-for="(row, x) in boardStatus" :key="x">
        <div v-for="(column, y) in row" :key="y">
          <SingleCell
            :key="`${x}:${y}`"
            :x="x" :y="y"
            :isCellAlive="alive(x, y)"
            @handleClick="toggleSingleCell($event)"
          ></SingleCell>
        </div>
      </div>
    </div>

    <Controls
      @toggleNextConwayCycle="nextConwayCycle()"
      @toggleRun="toggleRun()"
      :interval="interval"
      @toggleSpeed="toggleSpeed()"
      :speeds="speeds" :speed-id="speedId"
      @toggleResetCells="resetCells()"
      @toggleClearBoard="clearBoard()"
      :width="width" :height="height"
      @toggleChangeWidth="changeWidth($event)"
      @toggleChangeHeight="changeHeight($event)"
      @toggleResetWidth="resetWidth()"
      @toggleResetHeight="resetHeight()"
      :active-reset-button="originalBoardStatus.length"
      @toggleResetToStart="resetToOriginalBoardStatus()"
    ></Controls>

    <div class="text-xs flex flex-col sm:flex-row w-full mt-2 sm:mt-3">
      <div class="flex justify-center bg-grey-dark text-white p-3 pt-4 rounded">
        <span class="font-bold tracking-wide">Width</span>
        <div class="mx-2 relative" style="bottom:0.1rem">
          <input
            type="range" min="10" max="100" step="10"
            v-model="width" @change="resetCells()"
          />
        </div>
        <span>{{ width }}</span>
        <span
          @click="resetWidth()"
          class="ml-2 hover:text-red-dark cursor-pointer text-sm relative"
          style="bottom:0.2rem"
        >&#8634;</span>
      </div>
      <div class="flex justify-center bg-grey-dark text-white p-3 pt-4 rounded sm:ml-2 mt-2 sm:mt-0">
        <span class="font-bold tracking-wide">Height</span>
        <div class="mx-2 relative" style="bottom:0.1rem">
          <input
            type="range" min="10" max="100" step="10"
            v-model="height" @change="resetCells()"
          />
        </div>
        <span>{{ height }}</span>
        <span
          @click="resetHeight()"
          class="ml-2 hover:text-red-dark cursor-pointer text-sm relative"
          style="bottom:0.2rem"
        >&#8634;</span>
      </div>
    </div>

    <div>
      <button
        v-show="false"
        type="button"
        class="mt-2 bg-grey-dark hover:bg-grey-darker text-white py-2 px-4 text-sm rounded"
        @click="exportBoard()"
      >Export</button>
      <div>
        <div contenteditable v-show="exportBase64.length" v-text="exportBase64"
          class="mt-1 p-1 text-xs w-2/3 border boder-black bolder-solid bg-white whitespace-normal"
          style="word-wrap: break-word;"></div>
      </div>
    </div>

  </div>
</template>

<script>
import Stats from '@/components/Stats.vue'
import SingleCell from '@/components/SingleCell.vue'
import Controls from '@/components/Controls.vue'

export default {
  name: 'ConwayBoard',

  components: {
    Stats,
    SingleCell,
    Controls
  },

  data () {
    return {
      width: 20,
      height: 20,
      cycles: 0,
      cellsAlive: 0,
      boardStatus: [],
      originalBoardStatus: [],
      exportBase64: '',
      v: [],
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
    let { search } = new URL(window.location.href)
    let encodedBoard = search.replace('?b=', '')
    if (encodedBoard.length > 0) {
      this.import(encodedBoard)
    } else {
      this.resetCells()
    }
  },

  computed: {
    cellsCount () {
      return this.width * this.height
    }
  },

  methods: {
    alive (x, y) {
      return this.boardStatus[x][y]
    },

    resetCells () {
      this.boardStatus = []
      for (var x = 0; x < this.width; x++) {
        this.boardStatus[x] = []
        for (var y = 0; y < this.height; y++) {
          this.boardStatus[x][y] = Math.floor(Math.random() * 4) ? 0 : 1
        }
      }
      this.cellsAlive = this.boardStatus.reduce((count, row) => count + row.filter(c => c).length, 0)
      this.cycles = 0
      this.originalBoardStatus = []
    },

    clearBoard () {
      this.boardStatus = []
      for (var x = 0; x < this.width; x++) {
        this.boardStatus[x] = []
        for (var y = 0; y < this.height; y++) {
          this.boardStatus[x][y] = 0
        }
      }
      this.cellsAlive = 0
      this.cycles = 0
      this.originalBoardStatus = []
    },

    toggleRun () {
      if (this.interval === undefined) {
        this.startCycle()
      } else {
        this.stopCycle()
      }
    },

    startCycle () {
      this.interval = setInterval(this.nextConwayCycle, this.speeds[this.speedId].milisecs)
    },

    stopCycle () {
      clearInterval(this.interval)
      this.interval = undefined
    },

    toggleSpeed () {
      this.speedId = this.speedId === this.speeds.length - 1 ? this.speedId = 0 : this.speedId + 1
      if (this.interval !== undefined) {
        this.stopCycle()
        this.startCycle()
      }
    },

    nextConwayCycle () {
      if (this.cycles === 0) {
        this.storeOriginalBoardStatus()
      }
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
          if (isAlive && (aliveNeighbours === 2 || aliveNeighbours === 3)) {
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
      if (this.cellsAlive === 0) {
        this.stopCycle()
      }
    },

    getAliveNeighbours (x, y) {
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

    toggleSingleCell ([x, y]) {
      this.boardStatus[x][y] = !this.boardStatus[x][y]
      this.cellsAlive = this.boardStatus[x][y] ? this.cellsAlive + 1 : this.cellsAlive - 1
    },

    resetWidth () {
      if (this.width === 20) return
      this.width = 20
      this.resetCells()
    },

    resetHeight () {
      if (this.height === 20) return
      this.height = 20
      this.resetCells()
    },

    storeOriginalBoardStatus () {
      this.originalBoardStatus = JSON.parse(JSON.stringify(this.boardStatus))
    },

    resetToOriginalBoardStatus () {
      this.boardStatus = JSON.parse(JSON.stringify(this.originalBoardStatus))
      this.cellsAlive = this.boardStatus.reduce((count, row) => count + row.filter(c => c).length, 0)
      this.cycles = 0
      this.originalBoardStatus = []
    },

    import (str) {
      const importStr = JSON.parse(atob(str))
      let checked = 0
      if (importStr.length % 10 === 0) {
        this.width = importStr.length
        for (let i = 0; i < importStr.length; i++) {
          if (importStr[i].length % 10 === 0) {
            if (i === 1) {
              this.height = importStr[i].length
            }
            checked++
          }
        }
      } else {
        return
      }

      if (checked === importStr.length) {
        this.boardStatus = importStr
        this.cellsAlive = this.boardStatus.reduce((count, row) => count + row.filter(c => c).length, 0)
        this.cycles = 0
      }
    },

    exportBoard () {
      this.exportBase64 = btoa(JSON.stringify(this.boardStatus))
      console.log(btoa(JSON.stringify(this.boardStatus)))
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
