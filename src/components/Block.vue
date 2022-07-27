<template>
  <div v-if="showBlock" 
    class="block" :class="strClass + ' '+ bindClass"
    @click.left="endGame"
  >
      <slot name="clickme">Click me default - from slot</slot><br/>
      <slot name="clickme2">Click2 me default</slot>
      <div class="small">
        <p>
            Block props >  <br/>
            strClass: {{ strClass }}, bindClass: {{ bindClass }}
        </p>
        <p>
            <span v-if="isGameFinished">delay: {{ delay }}</span><br/>
            startTime: {{ startTime }}<br/>
            endTime: {{ startTime }}<br/>
            reactionTime: {{ reactionTime }}<br/>
        </p>
      </div>
  </div>
</template>

<script>
export default {
  props: ['delay', 'isPlaying', 'strClass', 'bindClass'],
  emits: ['endGame'],
  data(){
      return {
        showBlock: false,
        blockClass: '',
        startTime: null,
        endTime: null,
        isGameFinished: false
      }      
  },
  computed:{
    reactionTime() {
        return this.endTime - this.startTime
    }
  },
  methods:{
      endGame() {
          console.log('endGame this.isPlaying', this.isPlaying)
          if(! this.isPlaying) return
          this.endTime = Date.now()
          this.isGameFinished = true
          this.$emit('endGame', this.reactionTime)
      }
  },

  mounted(){
      console.log('Vue event cycle hook: mounted')
      setTimeout(() => {
          this.showBlock = true
          this.startTime = Date.now()
          console.log('delay', this.delay)
      }, this.delay);
  },
  updated(){
      console.log('Vue event cycle hook: updated')
  },
  unmounted(){
      console.log('Vue event cycle hook: unmounted')
  }
}
</script>

<style>
  .block {
    width: 400px;
    height: 200px;
    border-radius: 10px;
    background: rgb(248, 3, 3);
    color: white;
    text-align: center;
    padding: 100px 20px;
    margin: 40px auto;
  }
  .block.big {
      font-size: 20px;
      font-weight: bold;
  }
  .block div.small {
      font-size: 12px;
      font-weight: normal;
  }
  .block.italic {
      font-style: italic;
  }
  .block[theme="blue"] {
      background: cornflowerblue;
  }
  .block p {
      text-align: left;
  }
</style>