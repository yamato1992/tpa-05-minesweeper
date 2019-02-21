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
      tiles: [],
    };
  },
  methods: {
    startGame: function() {
      this.initializeTiles();
    },
    initializeTiles: function() {
      this.tiles = [];
      for (let rowIndex = 0; rowIndex < ROW_SIZE; rowIndex += 1) {
        let row = [];
        for (let columnIndex = 0; columnIndex < COLUMN_SIZE; columnIndex += 1) {
          let tile = {
            mined: this.setMine(),
            state: 'unopened',
          };
          row.push(tile);
        }
        this.tiles.push(row);
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
      if (this.tiles[tile.row][tile.column].mined) {
        this.tiles[tile.row][tile.column].state = 'mine';
        // reveal all other tiles
      } else {
        const neighborTiles = collectNeighborsTileInfo(tile);
        const countMines = countNeighborsMines(neighborTiles);
        if (countMines > 0) {
          this.tiles[tile.row][tile.column].state = 'opened';
        } else {
          //  if there are no mines
          //    this.tiles[tile.row][tile.column].state = 'opened';
          //    (for each neighbor)
          //      (if the neighbor has not been opened yet)
          //        (open the neighbor)
        }
      }
    },

    /**
     * collect information on its neighbors
     * @function 
     * @param {Object} tile
     * @return {Object} neighborTiles
     */
    collectNeighborsTileInfo: function(tile) {
      // collect information on its neighbors
    },

    /**
     * count how many mines surrond the tile
     * @function
     * @param {Array} neighborTiles
     * @return {Number} countMines
     */
    countNeighborsMines: function() {
      // count how many mines surrond the tile
    },

    /**
     * reval other tiles
     * @function
     * @param {Object} tile
     * @return {undefine}
     */
    revalOtherTiles: function(tile) {
      // reval other tiles
    },
    setFlag: function(tile) {
      this.tiles[tile.row][tile.column].state = 'flagged';
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
