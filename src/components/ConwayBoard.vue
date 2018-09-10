<template>
  <div>
    <div id="ConwayBoard" class="flex">
      <div v-for="(row, x) in boardStatus">
        <div v-for="(column, y) in row">
          <SingleCell :key="`${x}:${y}`" :isCellAlive="alive(x, y)"></SingleCell>
        </div>
      </div>
    </div>
    <button
      type="button"
      class="mt-2 bg-blue hover:bg-blue-dark text-white py-2 px-4 rounded"
      @click="startGame()"
    >
      {{ btnText }}
    </button>
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
      size: 75,
      boardStatus: [],
      gameActive: false
    }
  },
  created() {
    for (var x = 0; x < this.size; x++) {
      this.boardStatus[x] = [];
      for (var y = 0; y < this.size; y++) {
        this.boardStatus[x][y] = Math.round(Math.random())
      }
    }
  },
  computed: {
    btnText: function() {
      return this.gameActive ? 'Stop' : 'Start'
    }
  },
  methods: {
    alive: function(x, y) {
      return this.boardStatus[x][y]
    },
    startGame: function() {
      this.gameActive = !this.gameActive;
    }
  }
}
</script>

<style scoped>

</style>
