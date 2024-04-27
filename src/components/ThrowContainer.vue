<template>
  <div class="throw-container">
    <button
      :disabled="rollDisabled"
      @click="roll"
    >{{ rollButtonText }}</button>
    <button
      :disabled="true"
    >{{ score }}</button>
  </div>
</template>

<script>
export default {
  props: {
    rollDisabled: { required: true },
    turn: { required: true },
    score: { required: true }
  },
  methods: {
    roll() {
      const roll = Math.floor(Math.random() * (7 - 1) + 1);
      this.rollButtonText = roll;
      this.$emit('onRoll', roll);
    },
    setRollButtonText() {
      this.rollButtonText = this.turn ? 'Roll' : 'Wait';
    }
  },
  data() {
    return {
      rollButtonText: 'Roll'
    };
  },
  mounted() {
    this.setRollButtonText();
  },
  watch: {
    turn() {
      this.setRollButtonText();
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