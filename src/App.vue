<template>
  <div id="app">
    <h1 class="title">Simon Says</h1>

    <div class="board" :class="isGameBlocked ? 'blocked' : ''">
      <div
        @click="userClickHandler(item.id)"
        class="board-item"
        :class="item.isHighlight ? 'active' : ''"
        :style="`background-color: ${item.color}`"
        v-for="item in blocks"
        :key="item.id"
      ></div>
    </div>

    <button class="start-game-btn" @click="startGameHandler()">Старт</button>

    <div class="round">Раунд: {{ round }}</div>

    <div>
      <select class="select" v-model="difficulty" :disabled="isGameStarted">
        <option v-for="(item, index) in difficultyObj" :key="index">{{ index }}</option>
      </select>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      difficultyObj: {
        Легкий: 1500,
        Средний: 1000,
        Сложный: 400,
      },
      difficulty: 'Легкий',
      userStep: 0,
      round: 0,
      isGameBlocked: true,
      isGameStarted: false,
      interval: null,
      blocks: [
        {
          id: 1,
          color: 'blue',
          isHighlight: false,
        },
        {
          id: 2,
          color: 'red',
          isHighlight: false,
        },
        {
          id: 3,
          color: 'yellow',
          isHighlight: false,
        },
        {
          id: 4,
          color: 'green',
          isHighlight: false,
        },
      ],
      gameData: {
        gameGenerated: [],
        userInput: [],
      },
    };
  },
  methods: {
    startGameHandler() {
      this.round = 0;
      this.isGameStarted = true;
      clearInterval(this.interval);
      this.nextRound();
    },

    nextRound() {
      this.gameData = {
        gameGenerated: [],
        userInput: [],
      };
      this.userStep = 0;
      this.round++;
      this.gameData.gameGenerated = this.createGameData();
      this.isGameBlocked = true;

      this.highlightItems();
    },

    highlightItems() {
      let i = 0;
      this.interval = setInterval(() => {
        const block = this.blocks.find((item) => item.id === this.gameData.gameGenerated[i]);
        block.isHighlight = true;
        setTimeout(() => {
          block.isHighlight = false;
        }, this.difficultyObj[this.difficulty] / 2);
        i++;
        if (i >= this.gameData.gameGenerated.length) {
          clearInterval(this.interval);
          this.isGameBlocked = false;
        }
      }, this.difficultyObj[this.difficulty]);
    },

    userClickHandler(id) {
      this.gameData.userInput.push(id);
      this.userStep++;

      const lastItem = this.gameData.userInput[this.gameData.userInput.length - 1];
      const block = this.blocks.find((item) => item.id === id);

      block.isHighlight = true;
      setTimeout(() => {
        block.isHighlight = false;
      }, 100);

      if (this.gameData.gameGenerated[this.userStep - 1] === lastItem) {
        if (this.userStep === this.gameData.gameGenerated.length) {
          this.nextRound();
        }
      } else {
        this.finishGame();
      }
    },

    finishGame() {
      this.isGameBlocked = true;
      this.isGameStarted = false;
      this.userStep = 0;
      this.round = 0;
      this.gameData = {
        gameGenerated: [],
        userInput: [],
      };
      alert('Игра закончена');
    },

    createGameData() {
      let arr = [];

      let i = 0;
      while (i < this.round) {
        let rand = 1 - 0.5 + Math.random() * (4 - 1 + 1);
        let randNum = Math.round(rand);
        arr.push(randNum);
        i++;
      }

      return arr;
    },
  },
};
</script>

<style lang="sass">
@import 'assets/styles/main.sass'

#app
  font-family: Avenir, Helvetica, Arial, sans-serif

.title
  text-align: center
  margin-bottom: 40px

.board
  display: flex
  flex-wrap: wrap
  width: 100%
  max-width: 300px
  margin: 0 auto 50px
  border-radius: 100%
  overflow: hidden
  &.blocked
    pointer-events: none

.board-item
  width: 50%
  overflow: hidden
  background: #000
  cursor: pointer
  opacity: .3
  &::before
    content: ''
    float: left
    padding-top: 100%
  &.active
    opacity: 1

.start-game-btn
  width: 100%
  max-width: 300px
  display: block
  border: none
  background-color: orangered
  padding: 10px 20px
  color: #ffffff
  margin: 0 auto
  cursor: pointer
  margin-bottom: 30px

.round
  margin-bottom: 30px
  text-align: center

.select
  width: 100%
  max-width: 300px
  display: block
  margin: auto
  padding: 10px 20px
</style>
