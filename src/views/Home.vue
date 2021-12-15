<template>
  <div class="home">
    <div id="text">{{ displayText.join("") }}<span id="cursor"></span></div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';

@Options({
  data() {
    return {
      words:['"How do you know I’m mad?" said Alice.', '"You must be," said the Cat,' , '"or you wouldn’t have come here.”'],
      displayText:[],
      currentWord:"",
      wordIdx: 0,
      timeoutSpeed: null,
      isWaitingNextWord: false,

    }
  },
  props:{
    title:String,
    speed:{
      type:Number,
      default:100
    },
    deleteSpeed:{
      type:Number,
      default:50
    },
     nextWordInterval: {
      type: Number,
      default: 1200
      },
    words:{
      type:Array,
      default:[]
    }
  },
  computed:{
    TYPE_SPEED(){ return this.speed },
    DELETE_SPEED(){ return this.deleteSpeed },
    timeoutSpeed(){ return this.TYPE_SPEED * 0.8 }
  },
  mounted(){
    this.start();
  },
  methods: {
    start() {
      if (this.words && this.words.length > 0) {
        this.currentWord = this.words[this.wordIdx].split('')
        this.timeoutSpeed = this.speed
        this.animate = setTimeout(this.type, this.timeoutSpeed)
      }
    },
    type() {
      // if typing...
      if (this.currentWord.length > 0) {
        this.displayText.push(this.currentWord.shift())
        // if done typing, wait for a while
      } else if (!this.isWaitingNextWord && this.currentWord.length === 0 && this.displayText.length === this.words[this.wordIdx].length) {
        this.timeoutSpeed = this.nextWordInterval
        this.isWaitingNextWord = true
        // if done typing, then delete
      } else if (this.currentWord.length === 0 && this.displayText.length > 0) {
        this.timeoutSpeed = this.deleteSpeed
        this.displayText.pop()
        // if done typing & deleting
      } else if (this.currentWord.length === 0 && this.displayText.length === 0) {
        // change words
        if (this.wordIdx < (this.words.length - 1)) {
          this.wordIdx++
        } else {
          // reset
          this.wordIdx = 0
        }

        this.timeoutSpeed = this.speed
        this.isWaitingNextWord = false
        this.currentWord = this.words[this.wordIdx].split('')
        this.displayText.push(this.currentWord.shift())
      }

      setTimeout(this.type, this.timeoutSpeed)
    },
  },
})

export default class Home extends Vue {}
</script>


<style scoped>
html, body {
  position: fixed;
  overflow: hidden;
  margin: 0;
  height: 100%;
}
.home{
  top:50%;
  left:50%;
  background-color: black;
  height: 100vh;
  width: 100%;
}

.text {

  color:#da190b;
  display: inline-block;
  vertical-align: middle;
  letter-spacing: 2px;
}

@keyframes blink-animation {
  to {
    visibility: hidden;
  }
}

.cursor {
  color:#03A062;
  display: inline-block;
  margin-left: -3px;
  animation: blink-animation 1s steps(2, start) infinite;
}


</style>
