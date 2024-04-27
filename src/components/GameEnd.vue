<template>
  <div class="gameOver">
    <div>
      <h2>{{ winner }}</h2>
      <p>{{ player1Score }} - {{ player2Score }}</p>
      <button
        @click="reset"
      >Reset</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    player1Score: { required: true },
    player2Score: { required: true },
    winner: { required: true },
  },
  methods: {
    reset() {
      this.$emit('onReset');
    }
  }
}
</script>

<style>
@import url('../styles/variables.css');
.gameOver {
  z-index: 10;
  position: absolute;
  width: 100%;
  height: 100%;

  background: linear-gradient(180deg, var(--light-color) 0%, rgba(0, 0, 0, 0.2) 100%);
}

.gameOver > div {
  display: flex;
  flex-direction: column;
  align-items: center;

  width: 100%;
  height: 100%;
  padding-top: 10em;
}

.gameOver h2,
.gameOver p {
  color: var(--accent-color);
}

.gameOver p {
  margin-top: 0.5em;
  font-size: var(--h2);
}

.gameOver button {
  margin-top: 1em;
}
</style>