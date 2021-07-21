<template>
  <div>
    <header class="header">
      <h1 class="header__text">Игра Саймон на Vue 2</h1>
    </header>
    <section class="container">
      <div
        class="top-left-block"
        v-on:click="clickedBox(1)"
        v-bind:class="{ active: isTopLeftActive }"
      >
        <audio id="audio1">
          <source
            src="https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"
            type="audio/ogg"
          />
        </audio>
      </div>
      <div
        class="top-right-block"
        v-on:click="clickedBox(2)"
        v-bind:class="{ active: isTopRigthActive }"
      >
        <audio id="audio2">
          <source
            src="https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"
            type="audio/ogg"
          />
        </audio>
      </div>
      <div
        class="bottom-left-block"
        v-on:click="clickedBox(3)"
        v-bind:class="{ active: isBottomLeftActive }"
      >
        <audio id="audio3">
          <source
            src="https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"
            type="audio/ogg"
          />
        </audio>
      </div>
      <div
        class="bottom-right-block"
        v-on:click="clickedBox(4)"
        v-bind:class="{ active: isBottomRightActive }"
      >
        <audio id="audio4">
          <source
            src="https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"
            type="audio/ogg"
          />
        </audio>
      </div>
    </section>
    <p class="round">Круг: {{ score }}</p>
    <div class="difficulty">
      Выбрать сложность:
      <label class="difficulty__type"
        ><input type="radio" name="difficulty" id="easy" checked /> Легкая
      </label>
      <label class="difficulty__type"
        ><input type="radio" name="difficulty" id="medium" /> Средняя
      </label>
      <label class="difficulty__type"
        ><input type="radio" name="difficulty" id="hard" /> Сложная
      </label>
    </div>
    <div class="button-wrapper">
      <button class="button" v-on:click="changeState()">Начать!</button>
    </div>
    <div class="message">{{ message }}</div>
  </div>
</template>

<script>
export default {
  name: "App",
  data: function () {
    return {
      userTurn: false,
      state: "Start",
      isPlaying: false,
      sequence: [],
      index: 0,
      isTopLeftActive: false,
      isTopRigthActive: false,
      isBottomLeftActive: false,
      isBottomRightActive: false,
      score: 0,
      message: null,
      timer: "",
      pause: 0
    };
  },
  methods: {
    changeState: function () {
      if (this.state === "Start") {
        this.state = "Stop";
        this.message = null;
        this.isPlaying = true;
        if (document.getElementById("easy").checked) {
          this.pause = 1500
        } else if (document.getElementById("medium").checked) {
          this.pause = 1000
        } else if (document.getElementById("hard").checked) {
          this.pause = 400
        }
        this.computerTurn();
      } else {
        this.resetGame();
      }
    },
    computerTurn: function () {
      const self = this;
      this.index = 0;
      this.sequence.push(this.getRandom());
      this.showSequence(function () {
        self.userTurn = true;
      });
    },
    clickedBox: function (box) {
      const self = this;
      if (this.state === "Start") {
        return;
      }
      this.clickEffect(box);
      const isCorrect = this.checkSequence(box);
      if (!isCorrect) {
        // If user clicks wrong box
        self.processGameOver();
        return;
      } else {
        // If the current click is correct
        if (this.index === this.sequence.length - 1) {
          // If total items in sequence clicked
          this.score++;
          setTimeout(function () {
            self.userTurn = false;
            self.computerTurn();
          }, 1000);
        } else {
          // Pending clicks exist
          this.index++;
        }
      }
    },
    processGameOver: function () {
      this.message = "Вы проиграли :(";
      this.resetGame();
    },
    getRandom: function () {
      //Utility method to get a random number from 1 to 4
      return Math.floor(Math.random() * 4 + 1);
    },
    checkSequence: function (box) {
      return this.sequence[this.index] === box;
    },
    resetGame: function () {
      this.userTurn = false;
      this.state = "Start";
      this.score = 0;
      this.isPlaying = false;
      this.sequence = [];
      this.index = 0;
    },
    showSequence: function (callback) {
      const self = this;
      let i = 0;
      this.timer = setInterval(function () {
        if (i >= self.sequence.length) {
          self.stopInterval();
        }
        self.clickEffect(self.sequence[i]);
        i++;
      }, this.pause);
      callback();
    },
    stopInterval: function () {
      clearInterval(this.timer);
    },
    clickEffect: function (box) {
      //This method takes in a box number as parameter -> toggles its class as active -> plays the audio file -> reverts the class back
      const self = this;
      const audio1 = document.getElementById("audio1");
      const audio2 = document.getElementById("audio2");
      const audio3 = document.getElementById("audio3");
      const audio4 = document.getElementById("audio4");
      switch (box) {
        case 1:
          this.isTopLeftActive = true;
          audio1.play();
          setTimeout(function () {
            self.isTopLeftActive = false;
          }, 100);
          break;
        case 2:
          this.isTopRigthActive = true;
          audio2.play();
          setTimeout(function () {
            self.isTopRigthActive = false;
          }, 100);
          break;
        case 3:
          this.isBottomLeftActive = true;
          audio3.play();
          setTimeout(function () {
            self.isBottomLeftActive = false;
          }, 100);
          break;
        case 4:
          this.isBottomRightActive = true;
          audio4.play();
          setTimeout(function () {
            self.isBottomRightActive = false;
          }, 100);
          break;
      }
    },
  },
};
</script>

<style lang="scss">
* {
  color: rgb(58, 52, 85);
  font: 25px Arial, sans-serif;
  margin: 0;
  padding: 0;
}
.container {
  max-width: 200px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: 1fr 1fr;
}
.header__text {
  text-align: center;
  font-size: 30px;
}
@mixin box-style {
  width: 100px;
  height: 100px;
  margin: 3px;
  border-radius: 10px;
  opacity: 0.7;
}
.top-left-block {
  background: red;
  @include box-style;
}
.top-right-block {
  background: rgb(255, 196, 0);
  @include box-style;
}
.bottom-left-block {
  background: blue;
  @include box-style;
}
.bottom-right-block {
  background: green;
  @include box-style;
}
.top-left-block:hover,
.top-right-block:hover,
.bottom-left-block:hover,
.bottom-right-block:hover {
  box-shadow: 0px 0px 5px 3px #5e5b5a;
}

.round {
  text-align: center;
}
.difficulty {
  margin: 10px auto;
  display: flex;
  flex-direction: column;
  max-width: 300px;
}
.difficulty__type {
}
.button-wrapper {
  display: flex;
  justify-content: center;
}
.button {
  width: 150px;
  height: 50px;
  border-radius: 10px;
  border: none;
  cursor: pointer;
  background: rgb(208, 219, 231);
}
.message {
  text-align: center;
}
.active {
  opacity: 1;
}
</style>
