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
            {{ card.repetitions }} - {{ card.easinessFactor }} -
            {{ card.dueAt }}
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
          id: 1,
          front: "Algeria",
          back: "Algiers",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 2,
          front: "Angola",
          back: "Luanda",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 3,
          front: "Benin",
          back: "Porto-Novo",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 4,
          front: "Botswana",
          back: "Gaborone",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 5,
          front: "Burkina Faso",
          back: "Ouagadougou",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 6,
          front: "Burundi",
          back: "Bujumbura",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 7,
          front: "Cabo Verde",
          back: "Praia",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 8,
          front: "Cameroon",
          back: "Yaoundé",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 9,
          front: "Central African Republic",
          back: "Bangui",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 10,
          front: "Chad",
          back: "N'Djamena",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 11,
          front: "Comoros",
          back: "Moroni",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 12,
          front: "Democratic Republic of the Congo",
          back: "Kinshasa",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 13,
          front: "Republic of the Congo",
          back: "Brazzaville",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 14,
          front: "Djibouti",
          back: "Djibouti",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 15,
          front: "Egypt",
          back: "Cairo",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 16,
          front: "Equatorial Guinea",
          back: "Malabo",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 17,
          front: "Eritrea",
          back: "Asmara",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 18,
          front: "Eswatini",
          back: "Mbabane (administrative) / Lobamba (legislative, royal)",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 19,
          front: "Ethiopia",
          back: "Addis Ababa",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 20,
          front: "Gabon",
          back: "Libreville",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 21,
          front: "Gambia",
          back: "Banjul",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 22,
          front: "Ghana",
          back: "Accra",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 23,
          front: "Guinea",
          back: "Conakry",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 24,
          front: "Guinea-Bissau",
          back: "Bissau",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 25,
          front: "Ivory Coast (Côte d'Ivoire)",
          back: "Yamoussoukro (political) / Abidjan (economic)",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 26,
          front: "Kenya",
          back: "Nairobi",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 27,
          front: "Lesotho",
          back: "Maseru",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 28,
          front: "Liberia",
          back: "Monrovia",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 29,
          front: "Libya",
          back: "Tripoli",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 30,
          front: "Madagascar",
          back: "Antananarivo",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 31,
          front: "Malawi",
          back: "Lilongwe",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 32,
          front: "Mali",
          back: "Bamako",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 33,
          front: "Mauritania",
          back: "Nouakchott",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 34,
          front: "Mauritius",
          back: "Port Louis",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 35,
          front: "Morocco",
          back: "Rabat",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 36,
          front: "Mozambique",
          back: "Maputo",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 37,
          front: "Namibia",
          back: "Windhoek",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 38,
          front: "Niger",
          back: "Niamey",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 39,
          front: "Nigeria",
          back: "Abuja",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 40,
          front: "Rwanda",
          back: "Kigali",
          dueAt: null,
          repetitions: [],
        },
        {
          id: 41,
          front: "São Tomé and Príncipe",
          back: "São Tomé",
          dueAt: null,
          repetitions: [],
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
    card() {
      // Return the current flashcard
      return this.dueFlashcards[this.currentIndex];
    },
  },
  methods: {
    calculateRapidSR(grade) {
      // save answer in repetitons, with timestamp including seconds
      const answer = {
        timestamp: new Date(),
        answer: grade,
      };

      this.flashcards[this.currentIndex].repetitions.push(answer);
      // randomly set the due date to 1-100 seconds from now
      const seconds = Math.floor(Math.random() * 100) + 1;
      const dueAt = new Date(Date.now() + seconds * 1000).toISOString();
      this.flashcards[this.currentIndex].dueAt = dueAt;
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
      downloadAnchorNode.setAttribute("download", "flashcards.json");
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
