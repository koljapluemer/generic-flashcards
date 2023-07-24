<template>
  <div class="" v-if="card">
    <div class="paper">
      <div v-if="!isRevealed">{{ card.front }}</div>
      <div v-else>{{ card.back }}</div>

      current date: {{ Date.now() }} <br />
      due date: {{ card.dueAt }} <br />
    </div>

    <div class="flex p2 gap justify-center items-center">
      <button v-if="!isRevealed" @click="isRevealed = true">Reveal</button>
      <button v-if="isRevealed" @click="calculateRapidSR(0)">Wrong</button>
      <button v-if="isRevealed" @click="calculateRapidSR(1)">Correct</button>
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
      this.$emit("rate", isCorrect);
    },
  },
};
</script>
