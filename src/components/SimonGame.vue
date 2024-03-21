<script>
export default {
  data() {
    return {
      activeArray: [],
      baseArray: [
        'red',
        'green',
        'yellow',
        'blue',
      ],
      startButton: "Старт",
      count: 4,
      gameStatusDefault: false,
      gameStatusOnStart: false,
      gameStatusUser: false,
      interval: 1000,
      round: 0,
      sequenceInterval: null,
      strict: false,
      timerCasual: 1500,
      timerNormal: 1000,
      timerHard: 400,
      userArray: [],
      difficultyLevel: 'casual',
    }
  },

  computed: {
    // showRound() {
    //   return this.round;
    // }
  },

  watch: {
    // if strict changes in middle of game, reset it
    strict() {
      this.onStart();
    }
  },

  methods: {
    onStart() {
      this.gameStatusOnStart = true;
      this.startButton = "Заново";
      this.round = 1;
      this.activeArray = [];
      this.userArray = [];
      clearInterval(this.sequenceInterval)

      for (let i = 0; i < this.count; i++) {
        this.activeArray.push(this.baseArray[Math.floor(Math.random() * this.baseArray.length)]);
      }

      let currentIndex = 0;
      this.sequenceInterval = setInterval(() => {
        if (currentIndex >= this.activeArray.length) {
          clearInterval(this.sequenceInterval);
          return;
        }
        console.log(this.activeArray[currentIndex]);
        this.lightUp(this.$refs[this.activeArray[currentIndex]]);
        currentIndex++;
      }, this.selectTimer());
    },

    lightUp(tile) {
      tile.classList.add('active');
      setTimeout(() => {
        tile.classList.remove('active');
      }, 300);
    },

    selectTimer() {
      switch (this.difficultyLevel) {
        case 'casual':
          console.log(this.timerCasual)
          return this.timerCasual;
        case 'normal':
          return this.timerNormal;
        case 'hard':
          return this.timerHard;
      }
    },

  },
}
</script>

<template>
  <div class="wrapper">

    <h1>Simon The Game</h1>

    <div class="game-board">
      <div class="gaming-item">
        <ul>
          <li class="tile red" ref="red"></li>
          <li class="tile green" ref="green"></li>
          <li class="tile yellow" ref="yellow"></li>
          <li class="tile blue" ref="blue"></li>
        </ul>
      </div>
    </div>

    <div class="game-info">
      <h2>Раунд: <span>{{ round }}</span></h2>
      <button v-on:click='onStart'>{{ startButton }}</button>
      <p data-action="lose">Извините, вы проиграли (</p>
    </div>
    <div class="game-options">
      <h2>Уровни игры:</h2>
      <input type="radio" name="mode" value='casual' v-model="difficultyLevel" required>Легкий<br>
      <input type="radio" name="mode" value='normal' v-model="difficultyLevel" required>Средний<br>
      <input type="radio" name="mode" value='hard' v-model="difficultyLevel" required>Сложный<br>
    </div>
    <span>check: {{ difficultyLevel }}</span>

  </div>
</template>

<style scoped>

ul {
  list-style: none;
}

ul, li {
  padding: 0;
  margin: 0;
}

p[data-action="lose"] {
  display: none;
}

.active {
  opacity: 1 !important;
}

.wrapper {
  width: 540px;
  margin: 0 auto;
}

.gaming-item {
  background: #fff;
  position: relative;
  float: left;
  margin-right: 3em;
  width: 302px;
  height: 295px;
  -webkit-border-radius: 150px 150px 150px 150px;
  border-radius: 150px 150px 150px 150px;
  -moz-box-shadow: 2px 1px 12px #aaa;
  -webkit-box-shadow: 2px 1px 12px #aaa;
  -o-box-shadow: 2px 1px 12px #aaa;
  box-shadow: 2px 1px 12px #aaa;
}

.tile {
  opacity: 0.6;
  -webkit-transition: opacity 250ms ease;
  -moz-transition: opacity 250ms ease;
  -ms-transition: opacity 250ms ease;
  -o-transition: opacity 250ms ease;
  transition: opacity 250ms ease;
}

.red, .blue, .yellow, .green {
  height: 290px;
  -webkit-border-radius: 150px 150px 150px 150px;
  border-radius: 150px 150px 150px 150px;
  position: absolute;
  text-indent: 10000px;
}

.red:hover, .blue:hover, .yellow:hover, .green:hover {
  border: 2px solid black
}

.red {
  background: #FF5643;
  clip: rect(0px, 300px, 150px, 150px);
  width: 296px;
}

.blue {
  background: #0082ff;
  clip: rect(0px, 150px, 150px, 0px);
  width: 300px;
}

.yellow {
  background: #FEEF33;
  clip: rect(150px, 150px, 300px, 0px);
  width: 300px;
}

.green {
  background: #61ff3d;
  clip: rect(150px, 300px, 300px, 150px);
  width: 296px;
}

.game-info {
  margin-top: 90px;
}

.game-info button {
  width: 5em;
  box-sizing: border-box;
  font-size: 1.4em;
  -webkit-border-radius: 10px 10px 10px 10px;
  border-radius: 10px 10px 10px 10px;
  background: #8ce86d;
  border: none;
  padding: 0.3em 0.6em;
}

.game-info button:hover {
  background: #82d367;
}

.game-options h2 {
  margin-top: 30px;
  margin-bottom: 0;
}

.game-options input[type="radio"] {
  margin-right: 10px;
}

</style>