<template>
  <div id="app">
    <SwipeablePoiCardDeck
      :cards="visibleCards"
      :width="180"
      :height="350"
      @cardAccepted="handleCardAccepted"
    />
    <p>Current Top Card: {{ currentTopCard }}</p>
  </div>
</template>

<script>
import SwipeablePoiCardDeck from "@/components/SwipeablePoiCardDeck";

export default {
  name: "App",
  components: {
    SwipeablePoiCardDeck
  },

  data() {
    return {
      visibleCards: [
        { name: "Test 1" },
        { name: "Vue.js" },
        { name: "Webpack" },
        { name: "1" },
        { name: "2" },
        { name: "3" },
        { name: "4" },
        { name: "5" }
      ],
      currentTopCard: null
    };
  },

  methods: {
    handleCardAccepted(card) {
      this.currentTopCard = card.name;
    },
    handleCardRejected() {},
    handleCardSkipped() {}
  },
  mounted() {
    this.currentTopCard = this.visibleCards[0].name;
  }
};
</script>

<style lang="scss">
@import "./styles/mixins.scss";

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  text-align: center;
}
</style>
