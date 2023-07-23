<template>
  <div class="p4">
    <div class="">
      <div class="paper">
        <div v-if="!isRevealed">{{ card.front }}</div>
        <div v-else>{{ card.back }}</div>
      </div>

      <div class="flex p2 gap justify-center items-center">
        <button v-if="!isRevealed" @click="isRevealed = true">Reveal</button>
        <button v-if="isRevealed" @click="calculateRapidSR(0)">Wrong</button>
        <button v-if="isRevealed" @click="calculateRapidSR(1)">Correct</button>
      </div>
    </div>

    <!-- list all flashcards with all info for debugging -->
    <summary>
      <details>
        <ul>
          <li v-for="card in flashcards" :key="card.id">
            {{ card.front }} - {{ card.back }} - {{ card.interval }} -
            {{ card.dueAt }} - {{ card.repetitions.length }}
          </li>
        </ul>
        <!-- download button -->
        <button @click="download()">Download</button>
      </details>
    </summary>
  </div>
</template>

<script>
// import Flashcard from "./components/Flashcard.vue";

export default {
  components: {},
  data() {
    return {
      currentIndex: 0,
      isRevealed: false,
    };
  },
  created() {
    // Load flashcards from localStorage if available
    const savedFlashcards = JSON.parse(localStorage.getItem("flashcards"));
    // const savedFlashcards = false;

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
      console.log(this.flashcards);
    }

    this.nextCard();
  },
  computed: {
    initialFlashcards() {
      // Add your initial list of flashcards here
      return [
        {
          id: 42,
          front: "Senegal",
          back: "Dakar",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 43,
          front: "Seychelles",
          back: "Victoria",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 44,
          front: "Sierra Leone",
          back: "Freetown",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 45,
          front: "Somalia",
          back: "Mogadishu",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 46,
          front: "South Africa",
          back: "Pretoria (administrative) / Cape Town (legislative) / Bloemfontein (judicial)",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 47,
          front: "South Sudan",
          back: "Juba",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 48,
          front: "Sudan",
          back: "Khartoum",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 49,
          front: "Tanzania",
          back: "Dodoma (political) / Dar es Salaam (commercial)",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 50,
          front: "Togo",
          back: "Lomé",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 51,
          front: "Tunisia",
          back: "Tunis",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 52,
          front: "Uganda",
          back: "Kampala",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 53,
          front: "Zambia",
          back: "Lusaka",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
        {
          id: 54,
          front: "Zimbabwe",
          back: "Harare",
          dueAt: null,
          repetitions: [],
          interval: 1,
        },
      ];
    },
    dueFlashcards() {
      // Filter out flashcards that are due for review
      const currentDate = new Date()
      const dueCards =this.flashcards.filter(
        (card) =>
          (card.dueAt && new Date(card.dueAt) <= currentDate) || !card.dueAt
      );
      console.log(`found ${dueCards.length} due cards`);
    },
    dueFlashcardsWithoutNew() {
      // filter flashcards that have no repetitions
      return this.dueFlashcards.filter((card) => card.repetitions.length > 0);
    },
    card() {
      // Return the current flashcard
      return this.dueFlashcards[this.currentIndex];
    },
  },
  methods: {
    calculateRapidSR(grade) {
      // save answer in repetitons, with timestamp including seconds
      const answer = {
        timestamp: new Date().toISOString(),
        answer: grade,
      };

      this.flashcards[this.currentIndex].repetitions.push(answer);
      // if grade is 0, half interval (min: 1), else multiply with 2
      const interval =
        grade === 0
          ? Math.max(this.flashcards[this.currentIndex].interval / 2, 1)
          : this.flashcards[this.currentIndex].interval * 2;
      // add seconds to dueAt
      const dueAt = new Date();
      dueAt.setSeconds(dueAt.getSeconds() + interval);
      this.flashcards[this.currentIndex].interval = interval;
      // TODO: vue "reactiveness" apparently doesn't fucking extend to array contents?!?!
      this.flashcards[this.currentIndex].dueAt = dueAt.toISOString();
      // save the flashcards to localStorage
      localStorage.setItem("flashcards", JSON.stringify(this.flashcards));
      this.nextCard();
    },
    download() {
      // download flashcards as json
      const dataStr =
        "data:text/json;charset=utf-8," +
        encodeURIComponent(JSON.stringify(this.flashcards));
      const downloadAnchorNode = document.createElement("a");
      downloadAnchorNode.setAttribute("href", dataStr);
      downloadAnchorNode.setAttribute(
        "download",
        `flashcard_data_${Date.now().toISOString}.json`
      );
      document.body.appendChild(downloadAnchorNode); // required for firefox
      downloadAnchorNode.click();
      downloadAnchorNode.remove();
    },
    nextCard() {
      // Pick a random index from the dueFlashcards array
      // Set the current index to the new index
      this.currentIndex = Math.floor(Math.random() * this.dueFlashcards.length);
      this.isRevealed = false;
    },
  },
};
</script>
