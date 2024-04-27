<script setup>
import GameEnd from './components/GameEnd.vue';
import ThrowContainer from './components/ThrowContainer.vue';
import DiceContainer from './components/DiceContainer.vue';
</script>

<template>
  <div>
    <div class="page-wrapper">
      <h1 class="hiddenHeading">Knucklebones</h1>
      <GameEnd
        v-if="gameOver"
        :player1Score="player1Score"
        :player2Score="player2Score"
        :winner="winner"
        @onReset="resetGame"
      />
      <div class="game-container">
        <ThrowContainer
          :class="'player1'"
          :rollDisabled="!player1Turn || rolled"
          :turn="player1Turn"
          :score="player1Score"
          @onRoll="rollDice"
        />
        <DiceContainer
          :class="'player1'"
          :setRollEnabled="player1Turn && rolled"
          :board="player1Board"
          @onSetRoll="setRollOnBoard"
        />
        <ThrowContainer
          :class="'player2'"
          :rollDisabled="player1Turn || rolled"
          :turn="!player1Turn"
          :score="player2Score"
          @onRoll="rollDice"
        />
        <DiceContainer
          :class="'player2'"
          :setRollEnabled="!player1Turn && rolled"
          :board="player2Board"
          @onSetRoll="setRollOnBoard"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  methods: {
    toggleTurn() {
      // Toggle between players & set rolled = false
      this.player1Turn = !this.player1Turn;
      this.rolled = false;
    },
    rollDice(roll) {
      // Set passed roll as the current roll & set rolled = true
      this.currentRoll = roll;
      this.rolled = true;
    },
    setRollOnBoard(diceIndex) {
      // Use passed index of the dice to retrieve the column
      const column = this.getColumnNumber(diceIndex);

      // Update board with structuredClone to ensure change is reacted to
      // Check for any duplicate die in the opponent's matching column & remove them
      if (this.player1Turn) {
        const updatedBoard = structuredClone(this.player1Board);
        updatedBoard[diceIndex] = this.currentRoll;
        this.player1Board = updatedBoard;
        this.player2Board = this.removeOpponentColumnDuplicates(column, this.player2Board);
      } else {
        const updatedBoard = structuredClone(this.player2Board);
        updatedBoard[diceIndex] = this.currentRoll;
        this.player2Board = updatedBoard;
        this.player1Board = this.removeOpponentColumnDuplicates(column, this.player1Board);
      }

      // Calculate new scores after all board updates
      this.player1Score = this.setScore(this.player1Board);
      this.player2Score = this.setScore(this.player2Board);

      // Check for game end & toggle turn if game continues
      if (this.player1Board) {
        this.gameOver = this.checkGameEnd(this.player1Board);
      } else {
        this.gameOver = this.checkGameEnd(this.player2Board);
      }
      this.toggleTurn();
    },
    getColumnNumber (i) {
      switch (parseInt(i)) {
        case 0:
        case 3:
        case 6:
          return 0;
        case 1:
        case 4:
        case 7:
          return 1;
        case 2:
        case 5:
        case 8:
          return 2;
      }
    },
    removeOpponentColumnDuplicates (column, board) {
      const updatedBoard = structuredClone(board);
      for (let i = column; i < updatedBoard.length; i += 3) {
        if (updatedBoard[i] === this.currentRoll) {
          updatedBoard[i] = '';
        }
      }
      return updatedBoard;
    },
    setScore(board) {
      const getMap = (startIndex) => {
        const column = new Map();
        for (let i = startIndex; i < board.length; i += 3) {
          const dice = parseInt(board[i]) || 0;
          column.set(dice, (column.get(dice) || 0) + 1);
        }
        return column;
      }
      const addColumnScoreToScore = (column) => {
        for (const [key, value] of column.entries()) {
          score += ((key * value) * value);
        }
      }

      let score = 0;
      const column0 = getMap(0);
      const column1 = getMap(1);
      const column2 = getMap(2);
      addColumnScoreToScore(column0);
      addColumnScoreToScore(column1);
      addColumnScoreToScore(column2);
      return score;
    },
    checkGameEnd(board) {
      return board.indexOf('') === -1;
    },
    resetGame() {
      this.rolled = false;
      this.player1Turn = true;
      this.currentRoll = null;
      this.player1Score = 0;
      this.player2Score = 0;
      this.player1Board = ['', '', '', '', '', '', '', '', ''];
      this.player2Board = ['', '', '', '', '', '', '', '', ''];
      this.gameOver = false;
    }
  },
  data() {
    return {
      player1Turn: true,
      player: `1`,
      rolled: false,
      currentRoll: null,
      player1Board: ['', '', '', '', '', '', '', '', ''],
      player2Board: ['', '', '', '', '', '', '', '', ''],
      player1Score: 0,
      player2Score: 0,
      gameOver: false,
      winner: null
    };
  },
  watch: {
    player1Turn() {
      this.player = this.player1Turn ? `1` : `2`;
    },
    gameOver() {
      document.querySelector('.game-container').inert = this.gameOver;
      if (!this.gameOver) {
        return;
      }
      if (this.player1Score > this.player2Score) {
        this.winner = `Player 1 Wins`;
        return;
      }
      if (this.player1Score < this.player2Score) {
        this.winner = `Player 2 Wins`;
        return;
      }
      this.winner = `Draw`;
    }
  }
}
</script>

<style>
@import url('./styles/variables.css');

button:hover:not(:disabled),
.dice-container :disabled {
  border: thin solid var(--light-color);
  background-color: var(--accent-color);
  color: var(--accent-color-faded);
}

.page-wrapper {
  width: 100%;
  height: 100vh;
}

.game-container {
  display: grid;
  grid-template-columns: 50% 50%;
  grid-template-rows: 50% 50%;

  width: 90%;
  height: 45%;
}

.dice-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

.game-container > .dice-container {
  margin-top: 2em;
}

.dice-container button {
  margin: auto;
}
</style>
