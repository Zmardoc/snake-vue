<template>
  <div class="panel" ref="panel">
    <Keypress :key-code="13" event="keydown" @pressed="startPauseGame" />
    <Keypress
      v-for="direction in allDirections"
      :key="direction.key"
      :key-code="direction.key"
      event="keydown"
      @pressed="addDirection(direction.key)"
    />
    <block :size="size" :position="food" type="food" />
    <block
      v-for="(col, index) in cols"
      :key="col + '1'"
      :size="size"
      :position="{ x: index, y: 0 }"
      type="wall"
    ></block>
    <block
      v-for="(row, index) in rows"
      :key="row + '2'"
      :size="size"
      :position="{ x: 0, y: index }"
      type="wall"
    ></block>
    <block
      v-for="(row, index) in rows"
      :key="row + '3'"
      :size="size"
      :position="{ x: cols - 1, y: index }"
      type="wall"
    ></block>
    <block
      v-for="(col, index) in cols"
      :key="col + '4'"
      :size="size"
      :position="{ x: index, y: rows - 1 }"
      type="wall"
    ></block>
    <snake :size="size" :body="snakeBody" :message="snakeMessage" />
  </div>
</template>
<script>
import block from "./block";
import snake from "./snake";
export default {
  name: "panel",
  components: {
    block,
    snake,
    Keypress: () => import("vue-keypress")
  },
  props: {
    size: {
      type: Number,
      default: 15
    }
  },
  data: () => ({
    rows: 0,
    cols: 0,
    running: false,
    directionStack: [38],
    snakeBody: [],
    snakeMessage: "SNAKE - DĚKUJU VÁM ZA TY SUPR LÉTA KDE JINDE NEŽ V ČEZU",
    intervalStep: null,
    gameState: "paused",
    food: null,
    allDirections: [
      {
        type: "up",
        key: 38
      },
      {
        type: "right",
        key: 39
      },
      {
        type: "down",
        key: 40
      },
      {
        type: "left",
        key: 37
      }
    ]
  }),
  destroyed() {
    clearInterval(this.intervalStep);
  },
  mounted() {
    this.createWalls();
    const startingPosition = {
      x: Math.floor(this.cols / 2),
      y: Math.floor(this.rows / 2)
    };
    this.createSnake(startingPosition);
  },
  methods: {
    startPauseGame() {
      this.running = !this.running;
      if (this.running) {
        this.gameState = "running";
        this.intervalStep = setInterval(this.doStep, 200);
      } else {
        this.gameState = "paused";
        clearInterval(this.intervalStep);
      }
    },
    doStep() {
      this.createFoodIfNotExist();
      const headPosition = this.getHeadNextPosition();
      if (this.checkCollisionWithFood(headPosition)) {
        this.snakeBody.unshift(this.food);
        console.log(this.snakeBody);
        this.food = null;
      } else if (!this.checkCollisionWithSnake(headPosition)) {
        this.moveSnake(headPosition);
      } else {
        //TODO game over
        this.startPauseGame();
        this.gameState = "gameOver";
      }
    },
    checkCollisionWithFood(headPosition) {
      return this.food.x === headPosition.x && this.food.y === headPosition.y;
    },
    checkCollisionWithSnake(headPosition) {
      return this.snakeBody.some(
        block => block.x === headPosition.x && block.y === headPosition.y
      );
    },
    moveSnake(headPosition) {
      const { snakeBody } = this;
      snakeBody.unshift(headPosition);
      snakeBody.splice(-1, 1);
    },
    addDirection(directionKey) {
      let { directionStack } = this;
      const li = this.directionStack.length;

      if (
        li > 0 &&
        Math.abs(directionKey - directionStack[li - 1]) != 2 &&
        directionKey != directionStack[li - 1]
      ) {
        //37x39, 38x40
        directionStack.push(directionKey);
        //nedavat vic jak 2 do fronty
        if (li > 2) {
          directionStack.shift();
        }
      }
    },
    getHeadNextPosition() {
      const { snakeBody, directionStack } = this;
      const snakeHead = snakeBody[0];
      const direction = this.allDirections.find(
        d => d.key === directionStack[0]
      );
      if (directionStack.length > 1) {
        directionStack.shift();
      }
      switch (direction.type) {
        case "up":
          return {
            x: snakeHead.x,
            y: snakeHead.y - 1
          };
        case "down":
          return {
            x: snakeHead.x,
            y: snakeHead.y + 1
          };
        case "left":
          return {
            x: snakeHead.x - 1,
            y: snakeHead.y
          };
        default:
          return {
            x: snakeHead.x + 1,
            y: snakeHead.y
          };
      }
    },
    getRandomCoordinates() {
      const { cols, rows } = this;
      let x = Math.floor(Math.random() * cols - 1);
      let y = Math.floor(Math.random() * rows - 1);

      if (x <= 0) {
        x = 1;
      }
      if (y <= 0) {
        y = 1;
      }
      if (x >= rows) {
        x = rows - 1;
      }
      if (y >= cols) {
        y = cols - 1;
      }
      return { x: x, y: y };
    },
    createFoodIfNotExist() {
      if (!this.food) {
        this.food = Object.assign({}, this.getRandomCoordinates());
      }
    },
    createWalls() {
      const { size, $refs } = this;
      this.cols = Math.floor($refs.panel.clientWidth / size);
      this.rows = Math.floor($refs.panel.clientHeight / size);
    },

    createSnake(startingPosition) {
      this.snakeBody = [];
      this.snakeBody.push(startingPosition);
      for (let i = 1; i < 5; i++) {
        let block = {
          x: startingPosition.x,
          y: startingPosition.y + i
        };
        this.snakeBody.push(block);
      }
    }
  }
};
</script>
<style scoped>
.panel {
  position: fixed;
  padding: 0px;
  margin: 0px;
  top: 0;
  left: 0;
  bottom: 10px;
  right: 10px;
  width: 100%;
  height: 100%;
  background-color: black;
}
</style>
