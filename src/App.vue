<template>
  <div>
    <div class="page-wrapper">
      <h1 class="hiddenHeading">Knucklebones</h1>
      <div class="game-container">
        <div class="player1 throw-container">
          <button
            :disabled="!player1Turn"
            @click="rollDice"
          >Roll</button>
        </div>
        <div class="player1 dice-container">
          <button v-for="(dice, i) in player1Board"
            :key="`player1-${i}`"
            :class="`${i}`"
            :disabled="dice !== ''"
            @click="setRollOnBoard"
          >{{ dice }}</button>
        </div>
        <div class="player2 throw-container">
          <button
            :disabled="player1Turn"
            @click="rollDice"
          >Wait</button>
        </div>
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
      this.currentButton.innerHTML = 'Roll';
    },
    setActivePlayerRoll: function () {
      this.currentButton.innerHTML = 'Wait';
    },
    rollDice: function () {
      const roll = Math.floor(Math.random() * (7 - 1) + 1);
      this.currentRoll = roll;
      this.currentButton.innerHTML = this.currentRoll;
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

      this.rolled = false;
      this.toggleTurn();
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
      console.log(column);
      console.log(this.currentRoll);
      console.log(board);
      for (let i = column; i < board.length; i += 3) {
        console.log(i);
        if (board[i] === this.currentRoll) {
          board[i] = '';
        }
      }
      console.log(board);
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
      column1: [0, 3, 6],
      column2: [1, 4, 7],
      column3: [2, 5, 8]
    };
  },
  mounted() {
    this.setActivePlayerBoard();
  },
  watch: {
    player1Turn: function () {
      this.setActivePlayerRoll();
      this.player = this.player1Turn ? `1` : `2`;
      this.setActivePlayerBoard();
    },
    rolled: function () {
      this.currentButton.disabled = true;
      this.currentSection.inert = !this.rolled;
    },
    player1Board: function () {
      // current roll
      for (let i = 0; i < 3; i++) {
        // dice in column
        const dice1_index = i;
        const dice2_index = i + 3;
        const dice3_index = dice2 + 3;
        const player1_dieValues = [
          this.player1Board[dice1_index],
          this.player1Board[dice2_index],
          this.player1Board[dice3_index]
        ];
        const player2_dieValues = [
          this.player2Board[dice1_index],
          this.player2Board[dice2_index],
          this.player2Board[dice3_index]
        ];

        for (let j = i; j < this.player2Board.length; j + 3) {

        }
      }
    }
  }
}
</script>

<style>
@import url('./styles/styles.css');
h1.hiddenHeading {
  position: absolute;
  padding: 0;
  width: 1px;
  height: 1px;
  border: 0;
  clip: rect(1px, 1px, 1px, 1px);
  -webkit-clip-path: inset(0px 0px 99.9% 99.9%);
  clip-path: inset(0px 0px 99.9% 99.9%);
}

h2:not(aside h2) {
  padding-bottom: 1em;
}

button {
  width: 5em;
  height: 1.5em;
  border: thin solid var(--accent-color);
  border-radius: 5px;

  background-color: var(--accent-color-faded);
  color: var(--accent-color);
}

button:hover:not(:disabled),
.dice-container :disabled {
  border: thin solid var(--light-color);
  background-color: var(--accent-color);
  color: var(--accent-color-faded);
}

.game-container {
  display: grid;
  grid-template-columns: 50% 50%;
  grid-template-rows: 50% 50%;

  width: 90%;
  height: 65vh;
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

.throw-container {
  margin: auto;
}
</style>
