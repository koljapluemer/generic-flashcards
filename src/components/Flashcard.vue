<template>
  <div class="">
    <div class="paper">
      <div v-if="showFront">{{ card.front }}</div>
      <div v-else>{{ card.back }}</div>
    </div>

    <div class="flex p2 gap justify-center items-center">
      <button v-if="showFront" @click="revealCard">Reveal</button>
      <button v-if="!showFront" @click="onRate(false)">Wrong</button>
      <button v-if="!showFront" @click="onRate(true)">Correct</button>
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
