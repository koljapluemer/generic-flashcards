<template>
  <div class="paper">
    <div v-if="showFront" >{{ card.front }}</div>
    <div v-else >{{ card.back }}</div>
    <button @click="revealCard" >Reveal</button>
    <div v-if="!showFront" >
      <button @click="onRate(true)" >Correct</button>
      <button @click="onRate(false)" >Wrong</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    card: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      showFront: true,
    };
  },
  methods: {
    revealCard() {
      this.showFront = !this.showFront;
    },
    onRate(isCorrect) {
      this.$emit('rate', isCorrect);
    },
  },
};
</script>

<style>
/* Add any custom styles here if needed */
</style>
