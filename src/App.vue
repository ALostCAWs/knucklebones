<script setup>
import GameEnd from './components/GameEnd.vue';
import ThrowContainer from './components/ThrowContainer.vue';
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
          :rollDisabled="!player1Turn"
          :turn="player1Turn"
          :score="player1Score"
          @onRoll="rollDice"
        />
        <div class="player1 dice-container">
          <button v-for="(dice, i) in player1Board"
            :key="`player1-${i}`"
            :class="`${i}`"
            :disabled="dice !== ''"
            @click="setRollOnBoard"
          >{{ dice }}</button>
        </div>
        <ThrowContainer
          :class="'player2'"
          :rollDisabled="player1Turn"
          :turn="!player1Turn"
          :score="player2Score"
          @onRoll="rollDice"
        />
        <div class="player2 dice-container">
          <button v-for="(dice, i) in player2Board"
            :key="`player2-${i}`"
            :class="`${i}`"
            :disabled="dice !== ''"
            @click="setRollOnBoard"
          >{{ dice }}</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  methods: {
    toggleTurn: function () {
      this.player1Turn = !this.player1Turn;
    },
    setActivePlayerBoard: function () {
      this.currentButton = document.querySelector(`.player${this.player}.throw-container > button`);
      this.currentSection = document.querySelector(`.player${this.player}.dice-container`);
      document.querySelectorAll(`.dice-container`).forEach((container) => {
        container.inert = true;
      });
    },
    rollDice: function (roll) {
      this.currentRoll = roll;
      this.rolled = true;
    },
    setRollOnBoard: function (e) {
      const dice = e.target;
      const i = dice.className;
      const column = this.getColumn(i);

      if (this.player1Turn) {
        this.player1Board[i] = this.currentRoll;
        this.checkColumn(column, this.player2Board);
      } else {
        this.player2Board[i] = this.currentRoll;
        this.checkColumn(column, this.player1Board);
      }
      this.player1Score = this.setScore(this.player1Board);
      this.player2Score = this.setScore(this.player2Board);
      this.rolled = false;

      if (this.player1Board) {
        this.gameOver = this.checkGameEnd(this.player1Board);
      } else {
        this.gameOver = this.checkGameEnd(this.player2Board);
      }

      if (!this.gameOver) {
        this.toggleTurn();
      }
    },
    getColumn: function (i) {
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
    checkColumn: function (column, board) {
      for (let i = column; i < board.length; i += 3) {
        if (board[i] === this.currentRoll) {
          board[i] = '';
        }
      }
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
      this.player1Turn = true;
      this.rolled = false;
      this.currentRoll = null;
      this.player1Board = ['', '', '', '', '', '', '', '', ''];
      this.player2Board = ['', '', '', '', '', '', '', '', ''];
      this.player1Score = 0;
      this.player2Score = 0;
      this.gameOver = false;
      this.setActivePlayerBoard();
    }
  },
  data() {
    return {
      player1Turn: true,
      player: `1`,
      rolled: false,
      currentRoll: null,
      currentButton: null,
      currentSection: null,
      player1Board: ['', '', '', '', '', '', '', '', ''],
      player2Board: ['', '', '', '', '', '', '', '', ''],
      player1Score: 0,
      player2Score: 0,
      gameOver: false,
      winner: null
    };
  },
  mounted() {
    this.setActivePlayerBoard();
  },
  watch: {
    player1Turn: function () {
      this.player = this.player1Turn ? `1` : `2`;
      this.setActivePlayerBoard();
    },
    rolled: function () {
      this.currentButton.disabled = true;
      this.currentSection.inert = !this.rolled;
    },
    gameOver: function () {
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
