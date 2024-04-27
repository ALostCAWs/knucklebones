<template>
  <div class="dice-container" :inert="!setRollEnabled">
    <button v-for="(dice, i) in board"
      :key="`${i}`"
      :class="`${i}`"
      :disabled="dice !== ''"
      @click="setRoll"
    >{{ dice }}</button>
  </div>
</template>

<script>
export default {
  props: {
    setRollEnabled: { required: true },
    board: { required: true }
  },
  methods: {
    setRoll(e) {
      const dice = e.target;
      const diceIndex = dice.className;
      this.$emit('onSetRoll', diceIndex);
    }
  }
}
</script>

<style>
@import url('../styles/variables.css');

.throw-container {
  display: flex;
  flex-direction: column;
  margin: auto;
}

.throw-container button:first-of-type {
  margin-top: 2.5em;
}

.throw-container button:last-of-type {
  margin-top: 1em;
}
</style>