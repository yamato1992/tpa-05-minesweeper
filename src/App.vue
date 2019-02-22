<template>
  <div id="app">
    <button @click='startGame'>Start Game</button>
    <table class="minesweeper">
      <tr v-for='(row, rowIndex) in tiles' :key='rowIndex'>
        <Tile v-for='(tile, columnIndex) in row' :key='columnIndex'
          :row='tile.row' :column='tile.column' 
          :mined='tile.mined' :state='tile.state'
          @tileRightClicked='setFlag' @tileLeftClicked='openTile'>
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
            row: rowIndex,
            column: columnIndex,
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
      const tileObj = this.tiles[tile.row][tile.column];
      if (tileObj.mined) {
        tileObj.state = 'mine';
        this.showAll();
      } else {
        const neighbors = this.collectNeighborsInfo(tile.row, tile.column);
        const countMines = this.countNeighborsMines(neighbors);
        if (countMines > 0) {
          tileObj.state = `mine-neighbor-${countMines}`;
        } else {
          tileObj.state = 'opended';
          neighbors.forEach((neighbor) => {
            if (neighbor.state === 'unopened' && !neighbor.mined) {
              this.openTile(neighbor);
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
      let clickedTile = this.tiles[tile.row][tile.column];
      switch(clickedTile.state) {
      case 'unopened':
        clickedTile.state = 'flagged';
        break;
      case 'flagged':
        clickedTile.state = 'unopened';
        break;
      default:
        break;
      }
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
