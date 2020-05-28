<template>
  <div class="game">
    <header>
      <button class="play-btn" v-on:click="startGame()" :disabled="isPlaying">Play</button>
      <h2>Memory Card Game</h2>
      <h3>Time: {{time}} seconds.</h3>
    </header>
    <div class="card-container">
      <div
        class="card"
        v-for="(card,index) in cards"
        :key="index"
        @click="openCard(index)"
        :class="{cursor:isPlaying}"
      >
        <img v-if="card && card.active" :src="card.faceURL" alt="card-face-up" />
        <img v-else :src="cover" alt="card-back" />
        <!-- <p v-if="isPlaying">[..{{card.name}}..]</p> -->
      </div>
    </div>
  </div>
</template>

<script>
const settings = {
  time: 60,
  stepTime: 1,
  memorizeTime: 2,
  cardCount: 2,
  availableCards: [
    { name: "angular", active: false, faceURL: "cards/angular.png" },
    { name: "cSharp", active: false, faceURL: "cards/cSharp.png" },
    { name: "react", active: false, faceURL: "cards/react.png" },
    { name: "vuejs", active: false, faceURL: "cards/vue.png" },
    { name: "ember", active: false, faceURL: "cards/ember.png" },
    { name: "javaScript", active: false, faceURL: "cards/javaScript.png" }
  ],
  messages: {
    timeEnd: "Game Over Time elapsed",
    victory: remaining =>
      `Victory Solved for: ${settings.time - remaining} seconds`
  }
};

let timer = null;
let isMemorising = false;
let previousOpen = [];
const totalCardCount = settings.cardCount * settings.availableCards.length;

export default {
  name: "CardGame",
  data: function() {
    return {
      isPlaying: false,
      time: settings.time,
      cover: "cards/card-back.png",
      cards: new Array(totalCardCount)
    };
  },
  methods: {
    startGame: function() {
      this.reset();
      this.isPlaying = true;
      timer = setInterval(() => {
        this.time--;
      }, settings.stepTime * 1000);
    },

    reset: function() {
      clearTimeout(timer);
      this.shufle();
      this.time = 60;
      this.isPlaying = false;
      previousOpen = [];
    },

    shufle: function() {
      const shuffle = new Array(totalCardCount);
      for (const card of settings.availableCards) {
        for (let j = 0; j < settings.cardCount; j++) {
          let freeIndex = -1;
          while (freeIndex < 0 || (freeIndex >= 0 && shuffle[freeIndex])) {
            freeIndex = Math.floor(Math.random() * totalCardCount);
          }
          shuffle[freeIndex] = { ...card };
        }
      }

      this.cards = shuffle;
    },

    openCard(index) {
      const alreadyOpened = this.cards[index].active;
      if (!this.isPlaying || isMemorising || alreadyOpened) return;
      const isFirstCard = previousOpen.length === 0;
      if (isFirstCard) {
        previousOpen.push(index);
        this.cards[index].active = true;
      } else {
        const prevCardIndex = previousOpen[previousOpen.length - 1];
        const isBadGuess =
          this.cards[prevCardIndex].name !== this.cards[index].name;
        this.cards[index].active = true;
        previousOpen.push(index);
        if (isBadGuess) {
          isMemorising = true;
          setTimeout(() => {
            this.cards[index].active = false;
            previousOpen.forEach(x => {
              this.cards[x].active = false;
            });
            isMemorising = false;
            previousOpen = [];
          }, settings.memorizeTime * 1000);
          return;
        }

        if (previousOpen.length === settings.cardCount) {
          previousOpen = [];
        }
      }

      if (this.hasFinished) {
        setTimeout(() => {
          alert(settings.messages.victory(this.time));
          this.reset();
        }, 500);
      }
    }
  },

  computed: {
    hasFinished() {
      const containsClosed = this.cards.some(x => !x.active);
      return !containsClosed;
    }
  },
  watch: {
    time: function(_, newV) {
      if (newV === 0) {
        alert(settings.messages.timeEnd);
        this.reset();
      }
    }
  }
};
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
div.game {
  margin: 0 auto;
  max-width: 80em;
  height: 100%;
}

.game > header {
  display: flex;
  padding: 0 2em;
  flex-wrap: nowrap;
  justify-content: space-evenly;
  align-items: center;
}

.game > header > h2,
.game > header > h3 {
  text-align: center;
  color: var(--primary);
  /* flex:0 0 70%; */
}

.card-container {
  margin: 0 auto;
  max-width: 45em;
  border: 4px solid var(--primary);
  border-radius: 1em;
  min-height: 10em;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  padding: 0 1em;
}

div.card {
  flex: 0 1 22%;
  margin: 0.75% 0;
  display: block;
}

div.card > img {
  width: 100%;
  height: auto;
}

button.play-btn {
  background: burlywood;
  color: var(--primary);
  font-size: 2em;
  border: none;
  border-radius: 1rem;
  padding: 0.75rem 1rem;
  align-self: center;
}

.cursor {
  cursor: pointer;
}

button.play-btn:hover {
  background: rgba(255, 235, 205, 0.603);
  color: rgb(195, 83, 195);
  cursor: pointer;
}
</style>