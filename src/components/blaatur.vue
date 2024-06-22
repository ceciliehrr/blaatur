<template>
  <div class="cardTop">
    <svg
      width="350"
      height="219"
      viewBox="0 0 497 219"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
    >
      <defs>
        <linearGradient id="accent-gradient" x1="10%" y1="50%" x2="100%">
          <stop offset="0%" style="stop-color: rgb(var(--accent))"></stop>
          <stop
            offset="50%"
            style="stop-color: rgb(var(--accent-light))"
          ></stop>
          <stop offset="100%" style="stop-color: white"></stop>
        </linearGradient>
      </defs>
      <path
        fill-rule="evenodd"
        clip-rule="evenodd"
        d="M-38.5 196C-38.5 196 34 91 133.5 91C233 91 427 159 532.5 30C638 -99 518 236 518 236L-49 246.5L-38.5 196Z"
        fill="url(#accent-gradient)"
      ></path>
    </svg>
  </div>

  <main>
    <h1>Velkommen til<span class="text-gradient">Blåtur!</span></h1>
    <p class="instructions">
      Det er klart for blåtur!
      <code> Hurramegrundt!</code><br />
      <strong>Men først:</strong> Dere må guide sjåføren til neste ledetråd.
      <br />

      <strong>Lykke til!</strong>
      <button @click.prevent="openModal">Trykk her for første ledetråd</button>
    </p>
    <div class="timeline">
      <ul role="list" class="link-card-grid">
        <div v-for="(card, index) in cards" :key="index">
          <Card
            :key="isClosed[card.id]"
            :title="
              card.hasVisited && !isClosed[card.id]
                ? card.gratulererTitle
                : card.title
            "
            :body="card.body"
            :closed="isClosed[card.id]"
            :modalTitle="card.modalTitle"
            :modalBody="card.modalBody"
          />
        </div>
      </ul>
    </div>
    <Modal
      :title="firstModalTitle"
      :body="firstModalBody"
      v-if="showModal"
      @close="closeModal"
    />
  </main>
</template>

<script>
import { ref, onMounted } from "vue";
import Card from "../components/Card.vue";
import Modal from "../components/Modal.vue";
import { watch } from "vue";

export default {
  inheritAttrs: false,
  components: {
    Card,
    Modal,
  },
  setup() {
    const cards = [
      {
        href: "https://docs.astro.build/",
        title: "Første stoppested",
        gratulererTitle: "Gratulerer! Du er i Heddal!",
        body: "Finn den neste ledetråden. 🚀",
        id: "Gamlebyen",
        modalTitle: "Første stoppested",
        modalBody: "Neste destinasjone er..🚀",
      },
      {
        href: "https://astro.build/integrations/",
        title: "Andre stoppested",
        gratulererTitle: "Gratulerer!",
        body: "Finn den neste ledetråden her. 🚀",
        id: "Sørenga",
        modalTitle: "Andre stoppested",
        modalBody: "Neste destinasjon er..🚀",
      },
      {
        href: "https://astro.build/themes/",
        title: "tredje stoppested",
        gratulererTitle: "Gratulerer!",
        body: "Finn den neste ledetråden her. 🚀",
        id: "Berlin",
        modalTitle: "tredje stoppested",
        modalBody: "Neste destinasjon er..🚀",
      },
      {
        href: "https://astro.build/chat/",
        title: "fjerde stoppested",
        gratulererTitle: "Gratulerer! du er i Nissedal",
        body: "Siste stoppested ❤️",
        id: "Sognsvann",
        modalTitle: "fjerde stoppested",
        modalBody: "Neste destinasjon er..🚀",
      },
    ];

    // listen to the position
    let position = ref({ x: 0, y: 0 });
    watch(position, (newPosition) => {
      handlePosition(newPosition);
      console.log("Position changed to:", newPosition);
    });

    const isClosed = ref(
      cards.reduce((acc, card) => ({ ...acc, [card.id]: true }), {})
    );

    const resetHasVisited = () => {
      // Reset the localStorage item - run in console to reset the game
      localStorage.removeItem("hasVisited");
      // Also reset the local hasVisited object
      for (let id in this.hasVisited.value) {
        this.hasVisited.value[id] = false;
      }
    };

    const correctCoordinates = [
      { id: "Gamlebyen", latitude: 59.9076862, longitude: 10.771658 },
      { id: "Berlin", latitude: 52.520007, longitude: 13.404954 },
      { id: "Frogner", latitude: 59.922, longitude: 10.706 },
      { id: "Sognsvann", latitude: 59.989, longitude: 10.715 },
    ];

    // Define a tolerance in meters for the coordinates
    const tolerance = 5;
    const checkCoordinates = (position) => {
      const { latitude, longitude } = position.coords;
      console.log("User coordinates:", latitude, longitude);
      for (const coord of correctCoordinates) {
        const latDiff = Math.abs(latitude - coord.latitude);
        const lonDiff = Math.abs(longitude - coord.longitude);
        if (latDiff <= tolerance && lonDiff <= tolerance) {
          console.log("Location match found:", coord.id);
          return coord.id;
        }
      }
      return null; // Return null if no match found
    };

    const handlePosition = (position) => {
      const matchedId = checkCoordinates(position);
      if (matchedId) {
        // Update the hasVisited status for the matched card
        hasVisited.value[matchedId] = true;
        // Store the updated hasVisited object in localStorage
        localStorage.setItem("hasVisited", JSON.stringify(hasVisited.value));
        // Log the hasVisited object to the console
        console.log(hasVisited.value);
      }

      // Open all cards that have been visited
      for (let id in hasVisited.value) {
        if (hasVisited.value[id]) {
          console.log("Card is opened:", id);
          isClosed.value[id] = false;
        }
      }
    };

    let hasVisited = ref({});

    onMounted(() => {
      //use watchPosition
      //https://developer.mozilla.org/en-US/docs/Web/API/Geolocation/watchPosition
      // Initialize the hasVisited object with all cards set to false
      for (let card of cards) {
        hasVisited.value[card.id] = false;
      }
      // Update the hasVisited object from localStorage
      if (localStorage.getItem("hasVisited")) {
        const storedHasVisited = JSON.parse(localStorage.getItem("hasVisited"));
        for (let id in storedHasVisited) {
          hasVisited.value[id] = storedHasVisited[id];
        }
      }
      // Request the geolocation if any card hasn't been visited
      if (
        Object.values(hasVisited.value).includes(false) &&
        navigator.geolocation
      ) {
        navigator.geolocation.getCurrentPosition(handlePosition, (error) => {
          console.error("Error getting position:", error);
        });
      }
    });

    return {
      isClosed,
      cards,
    };
  },
  data() {
    return {
      showModal: false,
      firstModalTitle: "Kor ska vi reis hen?",
      firstModalBody:
        "I en by kjent for sin industrielle arv, finnes det et eldgammelt byggverk som står som en tidløs portal til middelalderen. Dette bygget, reist av treslagene fra omkringliggende skoger, har stått imot tidens tann og vitner om en svunnen tid. Hvor er vi på vei?",
    };
  },
  methods: {
    openModal() {
      console.log("openModal");
      this.showModal = true;
    },
    closeModal() {
      this.showModal = false;
    },
  },
};
</script>

<style lang="scss" scoped>
main {
  margin: auto;
  padding: 1rem;
  width: 100%;
  max-width: calc(100% - 2rem);
  color: white;
  font-size: 20px;
  line-height: 1.6;
}
button {
  background-color: rgb(var(--accent-dark));
  color: white;
  padding: 14px 20px;
  margin: 8px 0;
  border: none;
  cursor: pointer;
  width: 100%;
  min-width: 9.5rem;
  border-radius: 0.25rem;
  padding: 0 1rem;
  height: 3rem;
}
.astro-a {
  position: absolute;
  top: -32px;
  left: 50%;
  transform: translatex(-50%);
  width: 220px;
  height: auto;
  z-index: -1;
}
h1 {
  font-size: 4rem;
  font-weight: 700;
  line-height: 1;
  text-align: center;
  margin-bottom: 1em;
}
.text-gradient {
  background-image: var(--accent-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-size: 400%;
  background-position: 0%;
}
.instructions {
  margin-bottom: 2rem;
  border: 1px solid rgba(var(--accent-light), 25%);
  background: linear-gradient(
    rgba(var(--accent-dark), 66%),
    rgba(var(--accent-dark), 33%)
  );
  padding: 1.5rem;
  border-radius: 8px;
}
.instructions code {
  font-size: 0.8em;
  font-weight: bold;
  background: rgba(var(--accent-light), 12%);
  color: rgb(var(--accent-light));
  border-radius: 4px;
  padding: 0.3em 0.4em;
}
.instructions strong {
  color: rgb(var(--accent-light));
}
.link-card-grid {
  display: grid;
  gap: 2rem;
  padding: 0;
}
/* The actual timeline (the vertical ruler) */
.timeline {
  position: relative;
  max-width: 1200px;
  margin: 0 auto;
}
/* The actual timeline (the vertical ruler) */
.timeline::after {
  content: "";
  position: absolute;
  width: 6px;
  background-color: rgb(var(--accent-dark));
  top: 0;
  bottom: 0;
  left: 31px;
  margin-left: -31px;
  z-index: -1;
}

.cardTop {
  width: 100%;
  text-align: center;
}
</style>
