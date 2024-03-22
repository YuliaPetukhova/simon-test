<script>

const { Howl } = require('howler');
export default {
  data() {
    return {
      baseSequence: [
        'red',
        'green',
        'yellow',
        'blue',
      ],
      flashingSequence: [],
      userSequence: [],

      sounds: {
        'red': new Howl({
          src: ['/sounds/redSound2.mp3']
        }),
        'green': new Howl({
          src: ['/sounds/greenSound1.mp3']
        }),
        'yellow': new Howl({
          src: ['/sounds/yellowSound3.mp3']
        }),
        'blue': new Howl({
          src: ['/sounds/blueSound4.mp3']
        })
      },

      difficultyLevel: 'casual',
      difficultyLevels: {
        casual: 'casual',
        normal: 'normal',
        hard: 'hard',
      },

      gameStatusDefault: true,
      gameStatusFlashing: false,
      gameStatusAnswer: false,

      sequenceInterval: null,
      count: 0,
      round: 0,
      finalRound: 5,
      startButton: 'Старт',

      timerCasual: 1500,
      timerHard: 400,
      timerNormal: 1000,
      lightUpTimeout: 300,
    }
  },

  methods: {
    toDefaultState() {
      this.round = 0;
      this.count = 0;

      this.startButton = 'Заново';
      clearInterval(this.sequenceInterval);

      this.gameStatusDefault = true;
      this.gameStatusFlashing = false;
      this.gameStatusAnswer = false;
    },

    toFlashingState() {
      this.flashingSequence = [];
      this.userSequence = [];
      this.round++;
      this.count++;

      this.gameStatusDefault = false;
      this.gameStatusFlashing = true;
      this.gameStatusAnswer = false;
    },

    toAnswerState() {
      this.gameStatusDefault = false;
      this.gameStatusFlashing = false;
      this.gameStatusAnswer = true;
    },

    isAnswerState() {
      return this.gameStatusAnswer;
    },

    onChangeLevel() {
      this.toDefaultState();
    },

    onStart() {
      this.toDefaultState();

      this.startRound();
    },

    startRound() {
      this.toFlashingState();

      for (let i = 0; i < this.count; i++) {
        this.flashingSequence.push(this.baseSequence[Math.floor(Math.random() * this.baseSequence.length)]);
      }

      let currentIndex = 0;
      this.sequenceInterval = setInterval(() => {
        if (currentIndex >= this.flashingSequence.length) {
          clearInterval(this.sequenceInterval);
          return;
        }
        this.playSound(this.flashingSequence[currentIndex]);
        this.lightUp(this.$refs[this.flashingSequence[currentIndex]]);
        currentIndex++;
      }, this.selectTimer());

      this.toAnswerState();
    },

    onUserClick(tile) {
      if (this.isAnswerState()) {
        this.playSound(tile);
        this.lightUp(this.$refs[tile]);
        this.userSequence.push(tile);
        this.checkWinLose();
      }
    },

    checkWinLose() {
      for (let i = 0; i < this.userSequence.length; i++) {
        if (this.userSequence[i] !== this.flashingSequence[i]) {
          alert('Вы проиграли');
          this.toDefaultState();
          break;
        }
      }

      if (this.userSequence.length === this.flashingSequence.length) {
        this.startRound();
      }

      if(this.round === this.finalRound){
        alert('Вы выиграли');
        this.toDefaultState();
      }
    },

    lightUp(tile) {
      tile.classList.add('active');
      setTimeout(() => {
        tile.classList.remove('active');
      }, this.lightUpTimeout);
    },

    playSound(tile) {
      this.sounds[tile].play();
    },

    selectTimer() {
      switch (this.difficultyLevel) {

        case this.difficultyLevels.casual:
          return this.timerCasual;

        case this.difficultyLevels.normal:
          return this.timerNormal;

        case this.difficultyLevels.hard:
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
          <li class="tile red" ref="red" @click="onUserClick('red')"></li>
          <li class="tile green" ref="green" @click="onUserClick('green')"></li>
          <li class="tile yellow" ref="yellow" @click="onUserClick('yellow')"></li>
          <li class="tile blue" ref="blue" @click="onUserClick('blue')"></li>
        </ul>
      </div>
    </div>

    <div class="game-info">
      <h2>Раунд: <span>{{ round }}</span></h2>
      <button @click='onStart'>{{ startButton }}</button>
    </div>
    <div class="game-options">
      <h2>Уровни игры:</h2>
      <input type="radio" name="mode" value='casual' @click='onChangeLevel' v-model="difficultyLevel" required>Легкий<br>
      <input type="radio" name="mode" value='normal' @click='onChangeLevel' v-model="difficultyLevel" required>Средний<br>
      <input type="radio" name="mode" value='hard' @click='onChangeLevel' v-model="difficultyLevel" required>Сложный<br>
    </div>
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

.active {
  opacity: 1 !important;
}

.wrapper {
  width: 33.75em;
  margin: 0 auto;
}

.gaming-item {
  background: #fff;
  position: relative;
  float: left;
  margin-right: 3em;
  width: 18.9em;
  height: 18.5em;
  -webkit-border-radius: 9.4em 9.4em;
  border-radius: 9.4em 9.4em;
  -moz-box-shadow: 0.125em 0.063em 0.75em #aaa;
  -webkit-box-shadow: 0.125em 0.063em 0.75em #aaa;
  -o-box-shadow: 0.125em 0.063em 0.75em #aaa;
  box-shadow: 0.125em 0.063em 0.75em #aaa;
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
  height: 18.125em;
  -webkit-border-radius: 9.4em 9.4em;
  border-radius: 9.4em 9.4em;
  position: absolute;
  text-indent: 625em;
}

.red:hover, .blue:hover, .yellow:hover, .green:hover {
  border: 0.125em solid black
}

.red {
  background: #FF5643;
  clip: rect(0px, 18.75em, 9.4em, 9.4em);
  width: 18.5em;
}

.blue {
  background: #0082ff;
  clip: rect(0em, 9.4em, 9.4em, 0em);
  width: 18.75em;
}

.yellow {
  background: #FEEF33;
  clip: rect(9.4em, 9.4em, 18.75em, 0px);
  width: 18.75em;
}

.green {
  background: #61ff3d;
  clip: rect(9.4em, 18.75em, 18.75em, 9.4em);
  width: 18.5em;
}

.game-info {
  margin-top: 5.625em;
}

.game-info button {
  width: 5em;
  box-sizing: border-box;
  font-size: 1.4em;
  -webkit-border-radius: 0.625em 0.625em 0.625em 0.625em;
  border-radius: 0.625em 0.625em 0.625em 0.625em;
  background: #8ce86d;
  border: none;
  padding: 0.3em 0.6em;
}

.game-info button:hover {
  background: #82d367;
}

.game-options h2 {
  margin-top: 1.875em;
  margin-bottom: 0;
}

.game-options input[type="radio"] {
  margin-right: 0.625em;
}

</style>