<template>
  <!--img alt="Vue logo" src="./assets/logo.png"-->
  <!--HelloWorld msg="Welcome to Your Vue.js App"/-->
  <h1>Reaction timer</h1>
  <div class="scores">
    <p>
      Scores <span ref="scoresCount"></span> :
      <span v-for="(scoreItem, index) in scoresComputed" 
        :key="index"
        :class="{best: scoreItem.isBest}"
      >
        <template v-if="index > 0">, </template>
        {{ scoreItem.score }}
      </span>
    </p>
  </div>
  <button @click="start" :disabled="isPlaying == true">{{ startButtonText }}</button>
  <div v-if="!showBlock" class="block anticheat" @click.self="endGameAnticheat">
  </div>
  <Block 
    v-if="showBlock" 
    :delay="delay"
    :isPlaying="isPlaying"
    strClass="big" :bindClass="bindClass"
    theme="blue"
    @endGame="endGame"
  >
    <template v-slot:clickme />
    <template v-slot:clickme2>Click me - text from parent template v-slot</template>
  </Block>
</template>

<script>
import Block from './components/Block.vue'

export default {
  name: 'App',
  components: {
    Block
  },
  data() {
    return {
      isPlaying: false,
      showBlock: false,
      delay: null,
      bindClass: 'italic',
      scores:[],
      startButtonText: 'START',
      delayTimer: null,
    }
  },
  computed:{
    scoresCount(){
      let count = this.scores.length
      console.log('scoresCount count', count)
      return count ? '(' + count + ')' : ''
    },
    scoresComputed(){
      let minScore = 9999999999999
      for (let i = 0; i < this.scores.length; i++) {
        if(!this.scores[i].isNaN && this.scores[i] < minScore){
          minScore = this.scores[i]
        }
      }
      let scoresComputed = []
      for (let i = 0; i < this.scores.length; i++) {
        scoresComputed.push({
          score: this.scores[i],
          isBest: this.scores[i] === minScore
        })
      }
      return scoresComputed
    }
  },
  methods: {
    start(){
      this.delay = 500 + Math.random() * 3000
      this.showBlock = false
      this.isPlaying = true
      this.delayTimer = setTimeout(() => {
        this.showBlock = true
      }, this.delay);
      
    },
    endGame(reactionTime){
      console.log('parent received the end game reaction time', reactionTime)
      this.isPlaying = false
      this.scores.push(reactionTime)
      this.$refs.scoresCount.innerHTML = this.scoresCount
      this.startButtonText = 'TRY AGAIN'
    },
    endGameAnticheat(){
      clearTimeout(this.delayTimer);
      this.endGame('X')
      alert("You should only click when the blue block appears !")
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
  color: #444;
  margin-top: 60px;
}
.best{
  font-weight: bold;
  color: gold;
}
.block.anticheat{
  background: white;
}
</style>
