<template>
  <div class="p4">
    <Flashcard
      v-for="card in flashcards"
      :key="card.id"
      :card="card"
      @rate="handleRating"
      v-show="currentIndex === card.id - 1"
    />
    <div >
      <button
        @click="nextCard"
        
      >
        Next Card
      </button>
    </div>
  </div>
</template>

<script>
import Flashcard from "./components/Flashcard.vue";

export default {
  components: {
    Flashcard,
  },
  data() {
    return {
      flashcards: [
        { id: 1, front: "Algeria", back: "Algiers" },
        { id: 2, front: "Angola", back: "Luanda" },
        { id: 3, front: "Benin", back: "Porto-Novo" },
        { id: 4, front: "Botswana", back: "Gaborone" },
        { id: 5, front: "Burkina Faso", back: "Ouagadougou" },
        { id: 6, front: "Burundi", back: "Bujumbura" },
        { id: 7, front: "Cabo Verde", back: "Praia" },
        { id: 8, front: "Cameroon", back: "Yaoundé" },
        { id: 9, front: "Central African Republic", back: "Bangui" },
        { id: 10, front: "Chad", back: "N'Djamena" },
        { id: 11, front: "Comoros", back: "Moroni" },
        { id: 12, front: "Congo", back: "Brazzaville" },
        { id: 13, front: "Democratic Republic of the Congo", back: "Kinshasa" },
        { id: 14, front: "Djibouti", back: "Djibouti" },
        { id: 15, front: "Egypt", back: "Cairo" },
        { id: 16, front: "Equatorial Guinea", back: "Malabo" },
        { id: 17, front: "Eritrea", back: "Asmara" },
        { id: 18, front: "Eswatini (formerly Swaziland)", back: "Mbabane" },
        { id: 19, front: "Ethiopia", back: "Addis Ababa" },
        { id: 20, front: "Gabon", back: "Libreville" },
        { id: 21, front: "Gambia", back: "Banjul" },
        { id: 22, front: "Ghana", back: "Accra" },
        { id: 23, front: "Guinea", back: "Conakry" },
        { id: 24, front: "Guinea-Bissau", back: "Bissau" },
        { id: 25, front: "Ivory Coast (Côte d'Ivoire)", back: "Yamoussoukro" },
        { id: 26, front: "Kenya", back: "Nairobi" },
        { id: 27, front: "Lesotho", back: "Maseru" },
        { id: 28, front: "Liberia", back: "Monrovia" },
        { id: 29, front: "Libya", back: "Tripoli" },
        { id: 30, front: "Madagascar", back: "Antananarivo" },
        { id: 31, front: "Malawi", back: "Lilongwe" },
        { id: 32, front: "Mali", back: "Bamako" },
        { id: 33, front: "Mauritania", back: "Nouakchott" },
        { id: 34, front: "Mauritius", back: "Port Louis" },
        { id: 35, front: "Morocco", back: "Rabat" },
        { id: 36, front: "Mozambique", back: "Maputo" },
        { id: 37, front: "Namibia", back: "Windhoek" },
        { id: 38, front: "Niger", back: "Niamey" },
        { id: 39, front: "Nigeria", back: "Abuja" },
        { id: 40, front: "Rwanda", back: "Kigali" },
        { id: 41, front: "São Tomé and Príncipe", back: "São Tomé" },
        { id: 42, front: "Senegal", back: "Dakar" },
        { id: 43, front: "Seychelles", back: "Victoria" },
        { id: 44, front: "Sierra Leone", back: "Freetown" },
        { id: 45, front: "Somalia", back: "Mogadishu" },
        {
          id: 46,
          front: "South Africa",
          back: "Pretoria (administrative) / Cape Town (legislative) / Bloemfontein (judicial)",
        },
        { id: 47, front: "South Sudan", back: "Juba" },
        { id: 48, front: "Sudan", back: "Khartoum" },
        { id: 49, front: "Tanzania", back: "Dodoma" },
        { id: 50, front: "Togo", back: "Lomé" },
        { id: 51, front: "Tunisia", back: "Tunis" },
        { id: 52, front: "Uganda", back: "Kampala" },
        { id: 53, front: "Zambia", back: "Lusaka" },
        { id: 54, front: "Zimbabwe", back: "Harare" },
      ],

      currentIndex: 0,
    };
  },
  methods: {
    handleRating(isCorrect) {
      // Do something with the rating (e.g., update statistics)
      console.log("Card rated:", isCorrect);
    },
    nextCard() {
      // pick a random index that isnt the current 
      // set the current index to the new index
      this.currentIndex = Math.floor(Math.random() * this.flashcards.length);
    },
  },
};
</script>

<style>
/* Add any custom styles here if needed */
</style>
