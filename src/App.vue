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
          <button v-for="index in 9"
            @click="setRollOnBoard"
          ></button>
        </div>
        <div class="player2 throw-container">
          <button
          :disabled="player1Turn"
            @click="rollDice"
          >Roll</button>
        </div>
        <div class="player2 dice-container">
          <button v-for="index in 9"
            @click="setRollOnBoard"
          ></button>
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
      this.setActivePlayerBoard();
    },
    setActivePlayerBoard: function () {
      const player = this.player1Turn ? `1` : `2`;
      this.currentButton = document.querySelector(`.player${player}.throw-container > button`);
      this.currentSection = document.querySelector(`.player${player}.dice-container`);
      document.querySelectorAll(`.dice-container`).forEach((container) => {
        container.inert = true;
      });
    },
    rollDice: function () {
      const roll = Math.floor(Math.random() * (7 - 1) + 1);
      this.currentRoll = roll;
      this.currentButton.innerHTML = this.currentRoll;
      this.rolled = true;
    },
    setRollOnBoard: function (e) {
      const dice = e.target;
      dice.innerHTML = this.currentRoll;
      this.rolled = false;
      this.toggleTurn();
    }
  },
  data() {
    return {
      player1Turn: true,
      rolled: false,
      currentRoll: null,
      currentButton: null,
      currentSection: null
    };
  },
  mounted() {
    this.setActivePlayerBoard();
  },
  watch: {
    rolled: function () {
      this.currentButton.disabled = true;
      this.currentSection.inert = !this.rolled;
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

button:hover:not(:disabled) {
  border: thin solid var(--light-color);
  background-color: var(--accent-color);
}

.dice-container[inert] button,
button:disabled {
  background-color: red;
}

.game-container {
  display: grid;
  grid-template-columns: 50% 50%;
  grid-template-rows: 50% 50%;

  width: 90%;
  height: 70vh;
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
