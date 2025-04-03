<script setup>
import FieldComponent from '@/components/FieldComponent.vue'
import { Chess } from 'chess.js'
import { fieldMapper, CHESSBOARD_FIELDS } from '@/util/field-mapper.js'
import { reactive, computed, ref, watch } from 'vue'
import PieceComponent from '@/components/PieceComponent.vue'

const game = new Chess()
const flattenedBoard = computed(() => {
  return mappedBoard.value.flat().filter((piece) => piece)
})
let mappedBoard = ref([])
let selectedPiece = reactive([])
let boardSquares = ref([...CHESSBOARD_FIELDS])

function init() {
  mappedBoard.value = []
  game.board().forEach((rank) => {
    const mappedRank = rank.map((square) => {
      if (!square) {
        return null
      }
      const coordinates = fieldMapper(square.square)

      return {
        image: `src/assets/${square.color}${square.type}.png`,
        xPos: coordinates.xPos,
        yPos: coordinates.yPos,
        fieldName: square,
      }
    })

    mappedBoard.value.push(mappedRank)
  })
}

function clickPiece(piece) {
  if (!selectedPiece || !selectedPiece.fieldName) {
    selectedPiece = piece

    let moves = game.moves({
      square: selectedPiece.fieldName.square,
    })

    moves = moves.map((move) => {
      if(piece.fieldName.type !== 'p') {
        return move.slice(1);
      }
      return move;
    })

    boardSquares.value.forEach(field => {
      field.possibleMove = moves.includes(field.square);
    });

    init()
  } else {
    const move = `${selectedPiece.fieldName.square}-${piece.fieldName.square}`

    try {
      makeMove(move)
      boardSquares.value.forEach(field => field.possibleMove = false);

      selectedPiece = null
      init()
    } catch (e) {
      selectedPiece = null
      init()
    }
  }
}

function movePiece(field) {
  if (selectedPiece || selectedPiece.fieldName) {
    const move = `${selectedPiece.fieldName.square}-${field}`
    try {
      makeMove(move)
      boardSquares.value.forEach(field => field.possibleMove = false);
      selectedPiece = null
      init()
    } catch (e) {
      boardSquares.value.forEach(field => field.possibleMove = false);
      selectedPiece = null
      init()
    }
  }
}

function resetGame() {
  game.reset()
  selectedPiece = null
  init()
}

function checkState() {
  console.log(game.ascii())
}

function makeMove(move) {
  game.move(move)
  // Check for checkmate

  // Check for check
}
init()
</script>

<template>
  <div class="w-full pt-12 bg-slate-800 p-8 flex flex-col md:flex-row">
    <div class="actions my-4 flex flex-col gap-4">
      <button
        class="text-white font-bold rounded-xl border-pink-500 border-2 border-solid py-2 px-4"
        @click="checkState()"
      >
        CHECK STATE
      </button>
      <button
        class="text-white font-bold rounded-xl border-pink-500 border-2 border-solid py-2 px-4"
        @click="() => resetGame()"
      >
        RESET
      </button>
    </div>
    <div
      class="board grid grid-cols-8 w-full aspect-square border-pink-500 border-6 border-solid max-w-[1000px] m-auto rounded-xl relative"
    >
      <FieldComponent
        v-for="(square, index) in boardSquares"
        @click="movePiece(square.square)"
        :key="index"
        :isWhite="(Math.floor(index / 8) + (index % 8)) % 2 === 0"
        :fieldName="square.square"
        :possible-move="square.possibleMove"
      />
      <PieceComponent
        v-for="(piece, i) in flattenedBoard"
        @click="clickPiece(piece)"
        :key="i"
        :xPos="piece.xPos"
        :yPos="piece.yPos"
        :image="piece.image"
        :selected="selectedPiece?.fieldName?.square === piece.fieldName.square || false"
      />
    </div>

    <div class="moves grid grid-rows-2"></div>
  </div>
</template>

<style scoped>
.board {
  box-shadow:
    0 14px 28px rgba(0, 0, 0, 0.25),
    0 10px 10px rgba(0, 0, 0, 0.22);
}
</style>
