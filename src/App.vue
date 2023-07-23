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
  // Filter out flashcards that are due for review
  const currentDate = new Date();
  return flashcards.filter(
    (card) => (card.dueAt && new Date(card.dueAt) <= currentDate) || !card.dueAt
  );
});


const card = computed(() => {
  // Return the current flashcard
  return dueFlashcards.value[currentIndex.value];
});

function calculateRapidSR(grade) {
  // Save answer in repetitions, with a timestamp including seconds
  const answer = {
    timestamp: new Date().toISOString(),
    answer: grade,
  };

  flashcards[currentIndex.value].repetitions.push(answer);
  // If grade is 0, half interval (min: 1), else multiply with 2
  const interval =
    grade === 0
      ? Math.max(flashcards[currentIndex.value].interval / 2, 1)
      : flashcards[currentIndex.value].interval * 2;
  // Add seconds to dueAt
  const dueAt = new Date();
  dueAt.setSeconds(dueAt.getSeconds() + interval);
  flashcards[currentIndex.value].interval = interval;
  // Update dueAt using a new reactive object to trigger reactivity
  flashcards[currentIndex.value] = {
    ...flashcards[currentIndex.value],
    dueAt: dueAt.toISOString(),
  };
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
    `flashcard_data_${Date.now().toISOString()}.json`
  );
  document.body.appendChild(downloadAnchorNode); // Required for Firefox
  downloadAnchorNode.click();
  downloadAnchorNode.remove();
}

function nextCard() {
  // Pick a random index from the dueFlashcards array
  currentIndex.value = Math.floor(Math.random() * dueFlashcards.value.length);
  isRevealed.value = false;
}

// Call the nextCard function when the component is mounted
onMounted(nextCard);
</script>
