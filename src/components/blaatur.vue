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
      Det er klart for blåtur.
      <code> Hurramegrundt!</code><br />
      <strong>Men først:</strong> Dere må finne veien.
    </p>
    <div class="timeline">
      <ul role="list" class="link-card-grid">
        <div
          v-for="(card, index) in cards"
          :key="index"
          :class="['container', { 'container--closed': isClosed }]"
        >
          <Card
            :href="card.href"
            :title="card.title"
            :body="card.body"
            :closed="isClosed[card.id]"
            :id="card.id"
          />
        </div>
      </ul>
    </div>
  </main>
</template>

<script>
import { ref, onMounted } from "vue";
import Card from "../components/Card.vue";

export default {
  inheritAttrs: false,
  components: {
    Card,
  },
  setup() {
    const cards = [
      {
        href: "https://docs.astro.build/",
        title: "Første stoppested",
        body: "Finn den første ledetråden her. 🚀",
        id: "Gamlebyen",
      },
      {
        href: "https://astro.build/integrations/",
        title: "Andre stoppested",
        body: "Finn den andre ledetråden her. 🚀",
        id: "Sørenga",
      },
      {
        href: "https://astro.build/themes/",
        title: "tredje stoppested",
        body: "Finn den tredje ledetråden her. 🚀",
        id: "Berlin",
      },
      {
        href: "https://astro.build/chat/",
        title: "fjerde stoppested",
        body: "Siste stoppested ❤️",
        id: "Sognsvann",
      },
    ];

    const isClosed = ref(
      cards.reduce((acc, card) => ({ ...acc, [card.id]: true }), {})
    );

    let hasVisited;

    const correctCoordinates = [
      { id: "Gamlebyen", latitude: 59.9076862, longitude: 10.771658 },
      { id: "Berlin", latitude: 52.520007, longitude: 13.404954 },
      { id: "Frogner", latitude: 59.922, longitude: 10.706 },
      { id: "Sognsvann", latitude: 59.989, longitude: 10.715 },
      // Add more coordinates as needed
    ];

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
      console.log("Position received:", position);
      const matchedId = checkCoordinates(position);
      if (matchedId) {
        openCard(matchedId);
        // Set the flag in localStorage if available
        if (typeof localStorage !== "undefined") {
          localStorage.setItem("hasVisited", "true");
          console.log("Setting localStorage hasVisited to true");
        }
      } else {
        console.log("User is not in the correct location.");
      }
    };

    const openCard = (cardId) => {
      console.log("Card is opened:", cardId);
      isClosed.value[cardId] = false; // Open the card corresponding to cardId
    };

    onMounted(() => {
      console.log("Component mounted");

      // Check if the user has visited before
      if (typeof localStorage !== "undefined") {
        hasVisited = localStorage.getItem("hasVisited") === "true";
      }
      console.log("hasVisited:", hasVisited);
      console.log("navigator:", navigator);

      console.log("Requesting geolocation...");
      navigator.geolocation.getCurrentPosition(handlePosition, (error) => {
        console.error("Error getting position:", error);
      });
    });

    return {
      isClosed,
      cards,
    };
  },
};
</script>

<style scoped>
main {
  margin: auto;
  padding: 1rem;
  width: 100%;
  max-width: calc(100% - 2rem);
  color: white;
  font-size: 20px;
  line-height: 1.6;
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
}
/* Container around content */
.container {
  position: relative;
  background-color: inherit;
  padding-left: 36px;
}
/* The circles on the timeline */
.container::after {
  content: "";
  position: absolute;
  width: 25px;
  height: 25px;
  right: -17px;

  background-color: rgb(var(--accent));
  border: 4px solid rgb(var(--accent-light));
  top: 38px;
  border-radius: 50%;
  z-index: 1;
  left: -12px;
}
.container--closed::after {
  background-color: rgb(var(--accent-dark));
  border: 4px solid rgb(var(--accent-dark));
}

.cardTop {
  width: 100%;
  text-align: center;
}
</style>
