<template>
  <div id="app">
  <!-- Подключение аудио-дорожек -->
  <audio ref="sound1" src="https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"></audio>
  <audio ref="sound2" src="https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"></audio>
  <audio ref="sound3" src="https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"></audio>
  <audio ref="sound4" src="https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"></audio>
    <div class="container">
      <!-- Название игры -->
      <h1> Simon says </h1>
          <!-- Круг игры -->
          <div class="simon-game-ellipse">
          <div id="buttons">
            <div class="button" :class="{highlight: hlGreen}" id="green" @click="input(1)"></div>
            <div class="button" :class="{highlight: hlRed}" id="red" @click="input(2)"></div>
            <div class="button" :class="{highlight: hlYellow}" id="yellow" @click="input(3)"></div>
            <div class="button" :class="{highlight: hlBlue}" id="blue" @click="input(4)"></div>
          </div>
        </div>
        <!-- Информация о игре -->
      <div class="game-info">
            <h2>Раунд: <div id="counter">
          <span v-text="showError ? 'Извините, вы проиграли' : (showWin ? 'Поздравляем! Вы выиграли!' : displayCount)"></span>
        </div></h2>
          <button id="start" class="btn" @click="start()">Старт</button> <br>
          <small v-if="showError"> <button id="end" class="btn" @click="reset()">Начать заново </button> </small>
      </div>
      <!-- Уровень сложности -->
      <div class="level-of-complexity">
          <h2>Уровень сложности:</h2>
          <input type="radio" id="easy" v-model="picked" name="mode" value="0" checked>Лёгкий<br>
          <input type="radio" id="medium" v-model="picked" name="mode" value="1">Средний<br>
          <input type="radio" id="hard" v-model="picked" name="mode" value="2">Сложный
      </div>
    </div>
      <footer>
        <p>Made by Dupleeva Lydia</p>
      </footer>
  </div>
</template>

<script>

export default {
  name: 'App',
  data:() =>  ({
    started: false,
    count: 0,
    series: [],
    playingSeries: false,
    userInput: [],
    hlRed: false,
    hlGreen: false,
    hlYellow: false,
    hlBlue: false,
    allowInput: false,
    showError: false,
    showWin: false,
    totalToWin: 20,
    picked: 0
  }),
  computed: {
    displayCount() {
        return this.count
    }
  },
  methods:{
    reset(){
      this.started = false
      this.count = 0
      this.series = []
      this.userInput = []
      this.allowInput = false
      this.playingSeries = false
      this.showError = false
      this.showWin = false
      this.hlGreen = false
      this.hlRed = false
      this.hlYellow = false
      this.hlBlue = false
    },
    winner(){
      this.showWin = true
      let self = this
      let delay = 500
      this.h1Green = true
      for (let x = 1; x <= 6; x++) {
        setTimeout(function() { self.playTone(1) }, delay)
        if (x == 6)
          setTimeout(function() { self.hlGreen = false; self.showWin = false; self.reset() }, delay + 500)
        delay += 500
      }
    },
    input(tone){
      if (!this.allowInput)
       return
     this.playTone(tone)
     this.userInput.push(tone)
     // Check if this was the wrong input
     if (this.userInput[this.userInput.length - 1] != this.series[this.userInput.length - 1]) {
       this.userInput = []
       this.allowInput = false
       this.showError = true
    }
    if (this.userInput.length == this.series.length) {
        let self = this
        this.userInput = []
        // Check if we won (20 correct inputs)
        if (this.series.length == this.totalToWin) {
          // User has won the game
          setTimeout(this.winner, 500)
        } else {
          setTimeout(function() {
            self.addTone()
            self.playSeries()
          }, 1000)
        }
      }
  },
  start() {
        this.started = true
        this.addTone()
        this.playSeries()
    },
    addTone() {
      this.count++
      this.series.push(this.randomTone())
    },
    playSeries() {
      this.allowInput = false
      this.playingSeries = true
      let self = this
      let delay = 0
      if (Number(this.picked) == 0) delay = 1500
      if (Number(this.picked) == 1) delay = 1000
      if (Number(this.picked) == 2) delay = 400
      let clone_delay = delay
      this.series.forEach(function(tone, index, array) {
        if (index == array.length - 1)
          setTimeout(function() {
            if (self.started) {
            self.playTone(tone)
            self.allowInput = true
            self.playingSeries = false
            }
          }, delay)
        else
          setTimeout(function() { self.playTone(tone) }, delay)
        delay += clone_delay
      })
      this.playingSeries = false
    },
    clearHighlights() {
      this.hlGreen = false
      this.hlRed = false
      this.hlYellow = false
      this.hlBlue = false
    },
    // Plays the tone & highlights the color
    playTone(tone) {
      switch (tone) {
        case 1:
          this.$refs.sound1.pause()
          this.$refs.sound1.currentTime = 0
          this.$refs.sound1.play()
          this.hlGreen = true
          break;
        case 2:
          this.$refs.sound2.pause()
          this.$refs.sound2.currentTime = 0
          this.$refs.sound2.play()
          this.hlRed = true
          break;
        case 3:
          this.$refs.sound3.pause()
          this.$refs.sound3.currentTime = 0
          this.$refs.sound3.play()
          this.hlYellow = true
          break;
        case 4:
          this.$refs.sound4.pause()
          this.$refs.sound4.currentTime = 0
          this.$refs.sound4.play()
          this.hlBlue = true
          break;
      }
      if (!this.showWin)
        setTimeout(this.clearHighlights, 750)
    },
    stopTones() {
      this.$refs.sound1.pause()
      this.$refs.sound2.pause()
      this.$refs.sound3.pause()
      this.$refs.sound4.pause()
      this.$refs.sound1.currentTime = 0
      this.$refs.sound2.currentTime = 0
      this.$refs.sound3.currentTime = 0
      this.$refs.sound4.currentTime = 0
    },
    randomTone() {
      return Math.floor(Math.random() * 4) + 1
    }
}
}
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
.simon-game-ellipse{
  float: left;
}
#buttons {
  display: flex;
  flex-wrap: wrap;
  max-width: 440px;
  min-width: 440px;
  margin-left: 150px;
}
.button {
  cursor: pointer;
  min-width: 200px;
  min-height: 200px;
  max-width: 200px;
  max-height: 200px;
}
#green {
  background-color: #44f241;
  border-top-left-radius: 200px;
  box-shadow: inset 3px 3px 10px rgba(255,255,255,.3);
}
#blue {
  background-color: #51a1ed;
  border-bottom-right-radius: 200px;
  box-shadow: inset -3px -3px 10px rgba(255,255,255,.3);
}
#yellow {
  background-color: #f2ec3d;
  border-bottom-left-radius: 200px;
  box-shadow: inset 3px -3px 10px rgba(255,255,255,.3);
}
#red {
  background-color: #f23535;
  border-top-right-radius: 200px;
  box-shadow: inset -3px 3px 10px rgba(255,255,255,.3);
}
#green.highlight { background-color: #78f576; }
#red.highlight { background-color: #e87474; }
#blue.highlight { background-color: #7bb0e0; }
#yellow.highlight { background-color: #dce573; }

#start{
  width: 150px;
  height: 40px;
  border: none;
  border-radius: 20px;
  background-color: #51a1ed;
  font-size: 16px;
}
#start:hover{
  background-color: #2f90eb;
}
#end{
  width: 150px;
  height: 40px;
  border: none;
  border-radius: 20px;
  background-color: #51a1ed;
  font-size: 16px;
  margin-top: 20px;
}
#end:hover{
  background-color: #2f90eb;
}
footer {
  position : fixed;
  height : 50px;
  bottom : 0;
  width: 100%;
  text-align: center;
}
</style>
