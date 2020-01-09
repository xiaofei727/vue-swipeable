<template>
  <div class="swipeable-cards">
    <swipeable-poi-card
      v-for="(card, index) in allCards"
      :key="card.id"
      :card="card"
      :width="width"
      :height="height"
      :margin="margin"
      :offset="offset"
      :index="index"
      :is-current="index === 0"
      @cardAccepted="$emit('cardAccepted', allCards[1])"
      @hideCard="moveCardToBack"
    />
  </div>
</template>

<script>
import SwipeablePoiCard from "@/components/SwipeablePoiCard";

export default {
  data() {
    return {
      allCards: []
    };
  },
  components: {
    SwipeablePoiCard
  },
  mounted() {
    this.allCards = this.cards.map((item, index) => ({
      id: index + 1,
      name: item.name
    }));
  },
  methods: {
    moveCardToBack() {
      //console.log("moveCardToBack");
      this.allCards.push(this.allCards.shift());
    }
  },
  props: {
    cards: {
      type: Array,
      required: true
    },
    width: {
      type: Number
    },
    height: {
      type: Number
    },
    margin: {
      type: Number
    },
    offset: {
      type: Number
    }
  }
};
</script>
