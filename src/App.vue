<template>
  <div id="app">
    <button @click='startGame'>Start Game</button>
    <table class="minesweeper">
      <tr v-for='(row, rowIndex) in tiles' :key='rowIndex'>
        <Tile v-for='(column, columnIndex) in row' :key='columnIndex'
          :row='rowIndex' :column='columnIndex' 
          :mined='column.mined' :state='column.state'
          @tileRightClicked='setFlag'>
        </Tile>
      </tr>
    </table>
  </div>
</template>

<script>
const ROW_SIZE = 10;
const COLUMN_SIZE = 19;

import Tile from './components/Tile';

export default {
  name: 'App',
  data: function() {
    return {
      tiles: new Array(),
    };
  },
  methods: {
    startGame: function() {
      this.initializeTiles();
    },
    initializeTiles: function() {
      this.tiles = new Array(ROW_SIZE).fill(new Array(COLUMN_SIZE));
      for (let i = 0; i < ROW_SIZE; i += 1) {
        for (let j = 0; j < COLUMN_SIZE; j += 1) {
          this.tiles[i][j] = {
            mined: this.setMine(),
            state: 'unopened',
          };
        }
      }
    },
    setMine: function() {
      return Math.random() * 6 > 5;
    },
    /**
     * open a tile
     * @function
     * @param {Object} tile
     * @return {undefined}
     */
    openTile: function(tile) {
      // if the tiles is mined
      //  this.$set(this.tiles[tile.row][tile.column], 'state', 'mine');
      //  reveal all other tiles
      // if its not mined
      //  collect information on its neighbors
      //  count how many mines surround the tile
      //  if there are mines
      //    reveal the numbers of mines that surround the tile
      //  if there are no mines
      //      this.$set(this.tiles[tile.row][tile.column], 'state', 'opened');
      //    (for each neighbor)
      //      (if the neighbor has not been opened yet)
      //        (open the neighbor)
    },
    setFlag: function(tile) {
      this.$set(this.tiles[tile.row][tile.column], 'state', 'flagged');
      console.log(this.tiles[tile.row][tile.column]);
    },
  },
  components: {
    Tile,
  },
};
</script>

<style>
body {
  font-family: Helvetica, sans-serif;
  background: #333;
  color: #fff;
}

button {
  margin: 20px auto;
  display: block;
}

table.minesweeper {
  margin: auto;
  border: 1px solid #999;
  border-collapse: collapse;
  background-color: #ddd;
}

table.minesweeper td {
  width: 24px;
  height: 24px;
  background-size: cover;
}
</style>
