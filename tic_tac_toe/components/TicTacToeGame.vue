<script setup lang="ts">
import { ref, computed } from "vue";

type Player = "X" | "O" | "";
type Board = Player[][];

const initialBoard = (): Board => [
  ["", "", ""],
  ["", "", ""],
  ["", "", ""],
];

const board = ref<Board>(initialBoard());
const currentPlayer = ref<Player>("X");
const winner = ref<Player | "Draw" | "">("");
const moves = ref<number>(0);

const statusMessage = computed(() => {
  if (winner.value === "Draw") return "It's a draw!";
  if (winner.value === "X" || winner.value === "O") return `Player ${winner.value} wins!`;
  return `Current Player: ${currentPlayer.value}`;
});

function checkWinner(): Player | "Draw" | "" {
  // Check rows
  for (const row of board.value) {
    if (row[0] && row[0] === row[1] && row[1] === row[2]) return row[0];
  }
  // Check columns
  for (let i = 0; i < 3; i++) {
    if (
      board.value[0][i] &&
      board.value[0][i] === board.value[1][i] &&
      board.value[1][i] === board.value[2][i]
    ) {
      return board.value[0][i];
    }
  }
  // Check diagonals
  if (
    board.value[0][0] &&
    board.value[0][0] === board.value[1][1] &&
    board.value[1][1] === board.value[2][2]
  ) {
    return board.value[0][0];
  }
  if (
    board.value[0][2] &&
    board.value[0][2] === board.value[1][1] &&
    board.value[1][1] === board.value[2][0]
  ) {
    return board.value[0][2];
  }
  // Check draw
  if (moves.value === 9) return "Draw";
  return "";
}

function cellClick(row: number, col: number) {
  if (winner.value || board.value[row][col]) return;
  board.value[row][col] = currentPlayer.value;
  moves.value += 1;
  const result = checkWinner();
  if (result) {
    winner.value = result;
  } else {
    currentPlayer.value = currentPlayer.value === "X" ? "O" : "X";
  }
}

function resetGame() {
  board.value = initialBoard();
  currentPlayer.value = "X";
  winner.value = "";
  moves.value = 0;
}
</script>

<template>
  <div style="max-width: 380px; margin: 0 auto; margin-top: 2em;">
    <h2 style="margin-bottom: 12px;">Tic Tac Toe</h2>
    <div style="margin-bottom: 12px; font-weight: 500; font-size: 1.1em;">
      {{ statusMessage }}
    </div>
    <div style="display: grid; grid-template-rows: repeat(3, 1fr); gap: 4px;">
      <div v-for="(row, i) in board" :key="i" style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 4px;">
        <button
          v-for="(cell, j) in row"
          :key="j"
          @click="cellClick(i, j)"
          :disabled="cell || winner"
          style="width: 60px; height: 60px; font-size: 2rem; font-weight: 700; background: #fafafa; border: 2px solid #4caf50; color: #222; border-radius: 8px; transition: background 0.2s;"
          :style="{
            cursor: !cell && !winner ? 'pointer' : 'default',
            background: cell
              ? cell === 'X'
                ? '#e3f2fd'
                : '#fffde7'
              : '#fafafa'
          }"
        >
          {{ cell }}
        </button>
      </div>
    </div>
    <div style="margin-top: 14px;">
      <button v-if="winner" @click="resetGame" style="padding: 6px 16px; background: #4caf50; color: #fff; border: none; border-radius: 6px; font-weight: bold; font-size: 1em; cursor: pointer;">
        Restart Game
      </button>
    </div>
  </div>
</template>
