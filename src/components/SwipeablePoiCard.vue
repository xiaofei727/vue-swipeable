<template>
  <!-- make width / height configurable -->
  <div
    v-if="isShowing"
    ref="interactElement"
    :class="{
      isAnimating: isInteractAnimating,
      isCurrent: isCurrent
    }"
    class="swipeable-card"
    :style="{
      transform: transformString,
      width: this.width + 'px',
      height: this.height + 'px'
    }"
  >
    <h3 class="cardTitle">{{ card.name }}</h3>
  </div>
</template>

<script>
import interact from "interact.js";
const ACCEPT_CARD = "cardAccepted";
const REJECT_CARD = "cardRejected";
const SKIP_CARD = "cardSkipped";

export default {
  static: {
    interactMaxRotation: 15,
    interactOutOfSightXCoordinate: 200,
    interactOutOfSightYCoordinate: 350,
    interactYThreshold: 125,
    interactXThreshold: 75
  },

  props: {
    card: {
      type: Object,
      required: true
    },
    isCurrent: {
      type: Boolean,
      required: true
    },
    width: {
      type: Number
    },
    height: {
      type: Number
    },
    index: {
      type: Number
    }
  },

  data() {
    return {
      isShowing: true,
      isInteractAnimating: true,
      isInteractDragged: null,
      interactPosition: {
        x: 0,
        y: 0,
        rotation: 0,
        translateY: 0,
        scale: 0
      },
      isMovedToBack: false
    };
  },

  computed: {
    transformString() {
      if (!this.isInteractAnimating || this.isInteractDragged) {
        let { x, y, rotation, scale, translateY } = this.interactPosition;
        let resp;
        if (x >= 0 && y >= -20 && y <= 20) {
          if (!scale && !translateY)
            resp = `translate3D(${x}px, ${y}px, 0) rotate(${rotation}deg)`;
          else resp = `translateX(0px) translateY(0px) scale(${scale})`;
          return resp;
        } else return null;
      }
      return `translateY(${(this.index * this.height) / 30}px) scale(${1 - (this.index * 0.05)})`;
    }
  },

  mounted() {
    const element = this.$refs.interactElement;

    interact(element).draggable({
      onstart: () => {
        this.isInteractAnimating = false;
      },

      onmove: event => {
        const {
          interactMaxRotation,
          interactXThreshold
        } = this.$options.static;
        const x = this.interactPosition.x + event.dx;
        const y = this.interactPosition.y + event.dy;

        let rotation = interactMaxRotation * (x / interactXThreshold);

        if (rotation > interactMaxRotation) rotation = interactMaxRotation;
        else if (rotation < -interactMaxRotation)
          rotation = -interactMaxRotation;

        this.interactSetPosition({ x, y, rotation });
      },

      onend: () => {
        const { x } = this.interactPosition;
        const { interactXThreshold } = this.$options.static;
        this.isInteractAnimating = true;

        if (x > interactXThreshold) this.playCard(ACCEPT_CARD);
        else this.resetCardPosition();
      }
    });
  },

  beforeDestroy() {
    interact(this.$refs.interactElement).unset();
  },

  methods: {
    resetCardToBack() {
      setTimeout(() => {
        setTimeout(this.resetCardPosition, 200);
        setTimeout(this.sendCardToBack, 700);
        this.$emit("hideCard", this.card);
      }, 300);
    },
    playCard(interaction) {
      const {
        interactOutOfSightXCoordinate,
        interactOutOfSightYCoordinate,
        interactMaxRotation
      } = this.$options.static;

      this.isInteractDragged = true;

      switch (interaction) {
        case ACCEPT_CARD:
          this.interactSetPosition({
            x: interactOutOfSightXCoordinate,
            rotation: interactMaxRotation
          });
          this.$emit(ACCEPT_CARD);
          break;
        case REJECT_CARD:
          this.interactSetPosition({
            x: -interactOutOfSightXCoordinate,
            rotation: -interactMaxRotation
          });
          this.$emit(REJECT_CARD);
          break;
        case SKIP_CARD:
          this.interactSetPosition({
            y: interactOutOfSightYCoordinate
          });
          this.$emit(SKIP_CARD);
          break;
        default:
          break;
      }

      this.resetCardToBack();
    },

    interactSetPosition(coordinates) {
      const {
        x = 0,
        y = 0,
        rotation = 0,
        scale = 0,
        translateY = 0
      } = coordinates;
      this.interactPosition = { x, y, rotation, scale, translateY };
    },

    interactUnsetElement() {
      interact(this.$refs.interactElement).unset();
      this.isInteractDragged = true;
    },

    resetCardPosition() {
      this.interactSetPosition({ x: 0, y: 0, rotation: 0 });
    },
    sendCardToBack() {
      this.isInteractAnimating = true;
      this.isInteractDragged = false;
      this.resetCardPosition();
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../styles/index.scss";
</style>
