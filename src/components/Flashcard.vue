<template></template>

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
