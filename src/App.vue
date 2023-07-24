<template>
  <div class="p4">
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

    <span v-else>all done.</span>

    <!-- list all flashcards with all info for debugging -->
    <summary>
      <details>
        <ul>
          <li v-for="card in flashcards" :key="card.id">
            {{ card.front }} - {{ card.back }} - {{ card.interval }} - due:
            {{ card.dueAt }} - repeated: {{ card.repetitions.length }}
          </li>
        </ul>
        <!-- download button -->
        <button @click="download()">Download</button>
      </details>
    </summary>
  </div>
</template>

<script setup>
import { ref, reactive, computed, onMounted } from "vue";

const currentIndex = ref(0);
const isRevealed = ref(false);

const initialFlashcards = [
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
];

// Load flashcards from localStorage if available
const savedFlashcards = JSON.parse(localStorage.getItem("flashcards"));

const flashcards = reactive(
  savedFlashcards
    ? initialFlashcards.map((initialCard) => {
        const savedCard = savedFlashcards.find(
          (card) => card.id === initialCard.id
        );
        return savedCard ? { ...initialCard, ...savedCard } : initialCard;
      })
    : initialFlashcards
);

const dueFlashcards = computed(() => {
  console.log("computed due flashcards");
  // Filter out flashcards that are due for review
  const currentDate = Date.now();
  console.log("currentDate", currentDate);
  return flashcards.filter(
    (card) => (card.dueAt && card.dueAt <= currentDate) || !card.dueAt
  );
});

const card = computed(() => {
  // Check if due flashcards are available
  if (dueFlashcards.value.length === 0) {
    return null;
  }
  // Return the current flashcard
  return dueFlashcards.value[currentIndex.value];
});

function calculateRapidSR(grade) {
  // Save answer in repetitions, with a timestamp including seconds
  const answer = {
    timestamp: Date.now(),
    answer: grade,
  };

  flashcards[currentIndex.value].repetitions.push(answer);
  // If grade is 0, half interval (min: 1), else multiply with 2
  const interval =
    grade === 0
      ? Math.max(flashcards[currentIndex.value].interval / 2, 1)
      : flashcards[currentIndex.value].interval * 2;
  console.log("interval", interval);
  // Add seconds to dueAt
  const dueAt = Date.now() + interval * 1000;
  card.value.interval = interval;
  card.value.dueAt = dueAt;
  console.log(
    "check if we set the fucking dueAt value?",
    flashcards[currentIndex.value].dueAt
  );
  // TODO: for no reason, the wrong object is edited. Nice. No matter whether I use card or the indirect flashcards[] call. Holy shit.
  // Save the flashcards to localStorage
  localStorage.setItem("flashcards", JSON.stringify(flashcards));
  nextCard();
}

function download() {
  // Download flashcards as JSON
  const dataStr =
    "data:text/json;charset=utf-8," +
    encodeURIComponent(JSON.stringify(flashcards));
  const downloadAnchorNode = document.createElement("a");
  downloadAnchorNode.setAttribute("href", dataStr);
  downloadAnchorNode.setAttribute(
    "download",
    `flashcard_data_${Date.now()}.json`
  );
  document.body.appendChild(downloadAnchorNode); // Required for Firefox
  downloadAnchorNode.click();
  downloadAnchorNode.remove();
}

function nextCard() {
  // check if due flashcards are available
  // if not, console log
  if (dueFlashcards.value.length === 0) {
    console.log("No due flashcards available");
    return;
  }
  console.log("due", dueFlashcards.value);
  // Pick a random index from the dueFlashcards array
  currentIndex.value = Math.floor(Math.random() * dueFlashcards.value.length);
  isRevealed.value = false;
}

// Call the nextCard function when the component is mounted
onMounted(nextCard);
</script>
