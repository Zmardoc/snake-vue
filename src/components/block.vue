<template>
  <div :style="computedPosition" :class="type">{{ letter }}</div>
</template>
<script>
export default {
  name: "block",
  props: {
    letter: {
      type: String,
      default: ""
    },
    position: {
      x: {
        type: Number,
        default: 0
      },
      y: {
        type: Number,
        default: 0
      }
    },
    size: {
      type: Number,
      default: 15
    },
    type: {
      type: String,
      default: "wall"
    }
  },
  computed: {
    computedPosition() {
      if (this.position) {
        const { x, y } = this.position;
        const { size } = this;
        return `width: ${size}px; height: ${size}px; top: ${size *
          y}px; left: ${size * x}px; line-height: ${size}px;`;
      }
      return "";
    }
  }
};
</script>
<style scoped>
.wall,
.body,
.head,
.food {
  position: absolute;
}
.food {
  background-color: white;
  animation: moreBl 350ms infinite alternate;
}
.wall {
  background-color: #f24f00;
  border: 1px solid black;
}
.body {
  border: 1px solid #f24f00;
}
.body,
.head {
  background-color: black;
  color: white;
  text-align: center;
  font-family: "Arial Narrow Bold", sans-serif;
}
.head {
  border: 1px solid white;
}
@keyframes moreBl {
  from {
    opacity: 1;
  }
  to {
    opacity: 0.5;
  }
}
@keyframes shake {
  10%,
  90% {
    transform: translate3d(-1px, 0, 0);
  }

  20%,
  80% {
    transform: translate3d(2px, 0, 0);
  }

  30%,
  50%,
  70% {
    transform: translate3d(-4px, 0, 0);
  }

  40%,
  60% {
    transform: translate3d(4px, 0, 0);
  }
}
</style>
