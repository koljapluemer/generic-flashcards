<template>
  <div class="bg-white p-4 shadow-md rounded-lg">
    <div v-if="showFront" class="text-xl">{{ card.front }}</div>
    <div v-else class="text-xl">{{ card.back }}</div>
    <button @click="revealCard" class="mt-4 bg-blue-500 text-white px-4 py-2 rounded">Reveal</button>
    <div v-if="!showFront" class="mt-4">
      <button @click="onRate(true)" class="bg-green-500 text-white px-4 py-2 rounded">Correct</button>
      <button @click="onRate(false)" class="bg-red-500 text-white px-4 py-2 rounded">Wrong</button>
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
