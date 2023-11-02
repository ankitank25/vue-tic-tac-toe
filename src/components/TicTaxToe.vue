<script setup lang="ts">
import { ref, type Ref, computed } from 'vue';
const matrix = ref<Array<Array<string>>>([
  ["", "", ""],
  ["", "", ""],
  ["", "", ""],
]);

const player: Ref<string> = ref("X");

const winner: Ref<string> = ref("");

const isDraw = computed(() => {
    if(!winner.value && matrix.value.flat(1).filter(i => i !== "").length == 9) {
        return true;
    }
    return false;
});

function handleClick(row: number, col: number) {
  player.value = player.value === "X" ? "O" : "X";
  matrix.value[row][col] = player.value === "X" ? "O" : "X";

  winner.value = checkHorizontally() || checkVertically() || checkDiagonally() || "";
}
function checkRow(row: Array<string>) {
  if (row.filter((item) => item !== "").length) {
    const match = row.every((i) => i === row[0]);
    if (match) return row[0];
  }

  return false;
}

function checkHorizontally() {
  for (let row of matrix.value) {
    const match = checkRow(row);
    if (match) return match;
  }

  return false;
}

function checkVertically() {
  for (let c = 0; c < 3; c++) {
    let row: Array<string> = [];
    for (let r = 0; r <= matrix.value.length - 1; r++) {
      row.push(matrix.value[r][c]);
    }
    const match = checkRow(row);

    if (match) return match;
  }

  return false;
}

function checkDiagonally() {
  const leftRightMatch = checkRow([matrix.value[0][0], matrix.value[1][1], matrix.value[2][2]]);
  if (leftRightMatch) return leftRightMatch;

  const RightLeftMatch = checkRow([matrix.value[2][0], matrix.value[1][1], matrix.value[0][2]]);
  if (RightLeftMatch) return RightLeftMatch;
}
</script>
<template>
  <div class="w-96 m-auto text-center bg-gray-700 pb-8 text-gray-300">
    <div class="mt-8 text-2xl">
        <h4 class="text-4xl">Tic-Tac-Toe</h4>
        <div v-if="isDraw">It's a draw!</div>
        <div v-else-if="winner">Player <span :class="[winner == 'X'? 'text-green-700' : 'text-red-500', 'font-bold']">{{ winner }}</span> is a winner</div>
        <div v-else>Player <span :class="[player == 'X'? 'text-green-700' : 'text-red-500', 'font-bold']">{{ player }}</span>'s turn</div>
    </div>
    <div class="w-80 m-auto border border-gray-300 mt-8">
      <div
        v-for="(row, ri) in matrix"
        :key="`ttt-${ri}`"
        class="border-b border-gray-300 text-center last:border-b-0"
      >
        <template v-for="(n, ci) in row" :key="`ttt-col-${ci}`">
          <button
            @click.prevent="handleClick(ri, ci)"
            :disabled="!!n || !!winner"
            class="w-1/3 h-24 text-4xl even:border-x border-gray-300 leading-10 align-middle"
            :class="[n == 'X'? 'text-green-700' : 'text-red-500', {'bg-zinc-950' : !!winner}]"
          >
            {{ n }}
          </button>
        </template>
      </div>
    </div>
  </div>
</template>
