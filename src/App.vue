<template>
  <div class="p4">
    <Flashcard
      v-for="card in dueFlashcards"
      :key="card.id"
      :card="card"
      @rate="handleRating(card)"
      :ref="'flashcard-' + card.id"
      v-show="currentIndex === card.id - 1"
    />
  </div>

  <hr>

  <!-- list all flashcards with all info for debugging -->
  <ul>
    <li v-for="card in flashcards" :key="card.id">
      {{ card.front }} - {{ card.back }} - {{ card.interval }} - {{ card.repetitions }} - {{ card.easinessFactor }} - {{ card.dueAt }}
    </li>

  </ul>
</template>

<script>
import Flashcard from "./components/Flashcard.vue";

export default {
  components: {
    Flashcard,
  },
  data() {
    return {
      flashcards: [],
      currentIndex: 0,
    };
  },
  created() {
    // Load flashcards from localStorage if available
    const savedFlashcards = JSON.parse(localStorage.getItem("flashcards"));
    this.flashcards = savedFlashcards || this.initialFlashcards;
  },
  computed: {
    initialFlashcards() {
      // Add your initial list of flashcards here
      return [
        {
          id: 1,
          front: "Algeria",
          back: "Algiers",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 2,
          front: "Angola",
          back: "Luanda",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        // Add more countries and capitals here with their respective interval, repetitions, and easinessFactor values
      ];
    },
    dueFlashcards() {
      // Filter out flashcards that are due for review
      const currentDate = new Date();
      return this.flashcards.filter(
        (card) => (card.dueAt && new Date(card.dueAt) <= currentDate) || !card.dueAt
      );
    },
  },
  methods: {
    handleRating(card) {
      // Update the card's scores using the modified SM-2 algorithm
      if (card.repetitions === 0) {
        // First time, set the interval to 1
        card.interval = 1;
      } else if (card.repetitions === 1) {
        // Second time, set the interval to 6 days
        card.interval = 6;
      } else {
        // Use the modified SM-2 algorithm for subsequent repetitions
        card.interval *= card.easinessFactor;
      }

      // Increment the repetitions count
      card.repetitions++;

      // Calculate the next review date (dueAt) based on the interval and the current date
      const currentDate = new Date();
      const nextReviewDate = new Date(
        currentDate.getTime() + card.interval * 24 * 60 * 60 * 1000
      );
      card.dueAt = nextReviewDate.toISOString();

      // Save the updated flashcards to localStorage
      localStorage.setItem("flashcards", JSON.stringify(this.flashcards));

      // Move to the next card
      this.nextCard();
    },
    nextCard() {
      // Pick a random index from the dueFlashcards array
      // Set the current index to the new index
      this.currentIndex = Math.floor(Math.random() * this.dueFlashcards.length);
    },
  },
};
</script>
