<template>
  <table>
    <tr v-for="(row, index) in board2" :key="index">
      <td v-for="(color, index2) in row" :key="index2">
        <span v-if="color === NONE" style="background: green; color: green;" @click="onClick(index2 + 1, index + 1)">{{ "○" }}</span>
        <span v-if="color === BLACK" style="background: green; color: black;">{{ "●" }}</span>
        <span v-if="color === WHITE" style="background: green; color: white;">{{ "●" }}</span>
      </td>
    </tr>
  </table>
</template>

<script setup>
const BOARD_SIZE = 8
const NONE = 0
const BLACK = 1
const WHITE = 2

const turn = ref(BLACK)

const directions = ref([
  [-1, -1], [-0, -1], [+1, -1],
  [-1, -0], [+0, +0], [+1, +0],
  [-1, +1], [+0, +1], [+1, +1],
])

const board = ref([
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 2, 1, 0, 0, 0, 0],
  [0, 0, 0, 0, 1, 2, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
  [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
])

const board2 = computed(() => {
  return board.value.map((row, index) => {
    if (index === 0 || index === (BOARD_SIZE + 1)) return null
    return row.slice(1, -1)
  })
  .filter(row => {
    return row
  })
})

const enemyTurn = computed(() => {
  return BLACK + WHITE - turn.value
})

const onClick = (x, y) => {
  const flippablePlaces = isFlippable(x, y)

  if (flippablePlaces) {
    flippablePlaces.forEach(({x, y}) => {
      board.value[y][x] = turn.value
    })
    board.value[y][x] = turn.value
  } else {
    turn.value = enemyTurn.value
  }
  turn.value = enemyTurn.value
}

const isFlippable = (x, y) => {
  let places = []

  directions.value.forEach(([plusX, plusY]) => {
    const tmpPlaces = []
    while (board.value[y + plusY][x + plusX] === enemyTurn.value) {
      tmpPlaces.push({x: x + plusX, y: y + plusY})
      plusX += plusX
      plusY += plusY
    }
    if (board.value[y + plusY][x + plusX] === turn.value) {
      places = places.concat(tmpPlaces)
    }
  })

  return places.length ? places : null
}
</script>

<style>
* {
  box-sizing: border-box;
  cursor: pointer;
}

table {
  border-collapse: collapse;
}

table tr td {
  border: 2px black solid;
}

table td,
table td span {
  margin: 0;
  padding: 0;
  width: 40px;
  height: 40px;
  display: inline-block;
  font-size: 40px;
  line-height: 40px;
}
</style>
