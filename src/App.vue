<template>
  <div class="container mx-auto mt-8">
    <Flashcard
      v-for="card in flashcards"
      :key="card.id"
      :card="card"
      @rate="handleRating"
    />
    <div v-if="!showAllCards" class="mt-4">
      <button @click="nextCard" class="bg-blue-500 text-white px-4 py-2 rounded">Next Card</button>
    </div>
  </div>
</template>

<script>
import Flashcard from './components/Flashcard.vue';

export default {
  components: {
    Flashcard,
  },
  data() {
    return {
      flashcards: [
        { id: 1, front: 'Front of Card 1', back: 'Back of Card 1' },
        { id: 2, front: 'Front of Card 2', back: 'Back of Card 2' },
        { id: 3, front: 'Front of Card 3', back: 'Back of Card 3' },
        // Add more flashcards to this array
      ],
      currentIndex: 0,
      showAllCards: false,
    };
  },
  methods: {
    handleRating(isCorrect) {
      // Do something with the rating (e.g., update statistics)
      console.log('Card rated:', isCorrect);
    },
    nextCard() {
      this.currentIndex++;
      if (this.currentIndex >= this.flashcards.length) {
        this.showAllCards = true;
      }
    },
  },
};
</script>

<style>
/* Add any custom styles here if needed */
</style>
