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

  <hr />

  <!-- list all flashcards with all info for debugging -->
  <ul>
    <li v-for="card in flashcards" :key="card.id">
      {{ card.front }} - {{ card.back }} - {{ card.interval }} -
      {{ card.repetitions }} - {{ card.easinessFactor }} - {{ card.dueAt }}
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

    if (savedFlashcards) {
      // Merge saved flashcards with the initialFlashcards list to preserve any new flashcards
      this.flashcards = this.initialFlashcards.map((initialCard) => {
        const savedCard = savedFlashcards.find(
          (card) => card.id === initialCard.id
        );
        return savedCard ? { ...initialCard, ...savedCard } : initialCard;
      });
    } else {
      // If no saved flashcards, use the initialFlashcards list as is
      this.flashcards = this.initialFlashcards;
    }

    this.nextCard();
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
        {
          id: 3,
          front: "Benin",
          back: "Porto-Novo",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 4,
          front: "Botswana",
          back: "Gaborone",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 5,
          front: "Burkina Faso",
          back: "Ouagadougou",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 6,
          front: "Burundi",
          back: "Bujumbura",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 7,
          front: "Cabo Verde",
          back: "Praia",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 8,
          front: "Cameroon",
          back: "Yaoundé",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 9,
          front: "Central African Republic",
          back: "Bangui",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 10,
          front: "Chad",
          back: "N'Djamena",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 11,
          front: "Comoros",
          back: "Moroni",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 12,
          front: "Congo",
          back: "Brazzaville",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 13,
          front: "Congo, Democratic Republic of the",
          back: "Kinshasa",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 14,
          front: "Djibouti",
          back: "Djibouti",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
        {
          id: 15,
          front: "Egypt",
          back: "Cairo",
          interval: 1,
          repetitions: 0,
          easinessFactor: 2.5,
          dueAt: null,
        },
      ];
    },
    dueFlashcards() {
      // Filter out flashcards that are due for review
      const currentDate = new Date();
      return this.flashcards.filter(
        (card) =>
          (card.dueAt && new Date(card.dueAt) <= currentDate) || !card.dueAt
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
