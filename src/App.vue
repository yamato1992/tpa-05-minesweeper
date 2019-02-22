<template>
  <div id="app">
    <button @click='startGame'>Start Game</button>
    <table class="minesweeper">
      <tr v-for='(row, rowIndex) in tiles' :key='rowIndex'>
        <Tile v-for='(column, columnIndex) in row' :key='columnIndex'
          :row='rowIndex' :column='columnIndex' 
          :mined='column.mined' :state='column.state'
          @tileRightClicked='setFlag' @tileLeftClicked='openTile'>
        </Tile>
      </tr>
    </table>
  </div>
</template>

<script>
const ROW_SIZE = 5;
const COLUMN_SIZE = 9;

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
    openTile: function(tile) {
      if (this.tiles[tile.row][tile.column].mined) {
        this.tiles[tile.row][tile.column].state = 'mine';
        this.showAll();
      } else {
        const neighbors = this.collectNeighborsInfo(tile.row, tile.column);
        const countMines = this.countNeighborsMines(neighbors);
        if (countMines > 0) {
          this.tiles[tile.row][tile.column].state = `mine-neighbor-${countMines}`;
        } else {
          this.tiles[tile.row][tile.column].state = 'opended';
          neighbors.forEach((neighbor) => {
            if (neighbor.state === 'unopened') {
              // open neighor tile with recursive function
            }
          });
        }
      }
    },
    collectNeighborsInfo: function(rowIndex, columnIndex) {
      let neighbors = [];
      const patterns = [
        {row: 0, column: 1},
        {row: 0, column: -1},
        {row: 1, column: 0},
        {row: 1, column: 1},
        {row: 1, column: -1},
        {row: -1, column: 0},
        {row: -1, column: 1},
        {row: -1, column: -1},
      ];
      patterns.forEach((pattern) => {
        const neighborRowIndex = rowIndex + pattern.row;
        const neighborColumnIndex = columnIndex + pattern.column;
        if (neighborRowIndex >= 0 && neighborRowIndex < ROW_SIZE &&
          neighborColumnIndex >= 0 && neighborColumnIndex < COLUMN_SIZE) {
          neighbors.push(this.tiles[neighborRowIndex][neighborColumnIndex]);
        }
      });
      return neighbors;
    },
    countNeighborsMines: function(neighbors) {
      let mineCount = 0;
      neighbors.forEach((neighbor) => {
        mineCount += neighbor.mined ? 1 : 0;
      });
      return mineCount;
    },
    showAll: function() {
      this.tiles.forEach((row, rowIndex) => {
        row.forEach((tile, columnIndex) => {
          if (tile.mined) {
            tile.state = 'mine';
          } else {
            const neighbors = this.collectNeighborsInfo(rowIndex, columnIndex);
            const countMines = this.countNeighborsMines(neighbors);
            if (countMines > 0) {
              tile.state = `mine-neighbor-${countMines}`;
            } else {
              tile.state = 'opended';
            }
          }
        });
      });
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
