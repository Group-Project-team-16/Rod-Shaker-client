<template>
  <div class="container" id="game">
    <div class="wrapper">
      <div id="player"><img v-bind:src="require('../sprites/' + gif)" /></div>
      <div id="fishes" :style="'margin-top: ' + fishPos + 'rem' +'height: 20%'" >
        <div class="d-flex flex-column-reverse bd-highlight mr-3">
          <div v-for="(fish, i) in fishes" :key="i" class="bd-highlight"><img src="../sprites/fish.gif" /></div>
        </div>
      </div>
      <div id="playertwo"><img v-bind:src="require('../sprites/' + gif2)" /></div>
    </div>
    <div class="d-flex justify-content-between" id="container-score">
      <div
      v-if="userRole == dataUserPlayer1" 
      id="PlayeroneConsole">
        <button @click="narik(1)" class="btn btn-primary mb-5">Tarik Sist!!</button>
        <h1>My Score : {{scoreOne}}</h1>
        <h1>Score Another Player: {{scoreTwo}}</h1>
      </div>
      <div
      v-else-if="userRole == dataUserPlayer2" 
      id="PlayerTwoConsole">
        <button @click="narik(2)" class="btn btn-danger mb-5">Tarik Sist!!</button>
        <h1>My Score : {{scoreTwo}} </h1>
        <h1>Score Another Player: {{scoreOne}}</h1>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Game',
  data () {
    return {
      gif: 'idle-p1.gif',
      gif2: 'idle-p2.gif',
      timeBeforeStart: 0,
      fishPos: 17,
      scoreOne: 0,
      scoreTwo: 0,
      timeRand: 0,
      cekInterval: 100,
      fishes: 0,
      newscore1: 0
    }
  },
  methods: {
    narik (player) {
      if (player === 1) {
        if (this.timeBeforeStart < this.timeRand) {
          this.scoreOne = this.scoreOne - 0
        } else if (this.timeBeforeStart >= this.timeRand) {
          this.fishes = 1
          this.scoreOne = this.scoreOne + 10
          this.timeBeforeStart = 0
          this.gif = 'narik-p1.gif'
          this.gif2 = 'idle-p2.gif'
          setTimeout(() => {
            this.fishes = 0
            this.gif = 'idle-p1.gif'
          }, 1500)
        }
        let obj = {
                score: this.scoreOne,
                role: this.userRole
            }
          this.$socket.emit('narikSocket', obj)
          
      } else {
        if (this.timeBeforeStart < this.timeRand) {
          this.scoreTwo = this.scoreTwo - 0
        } else if (this.timeBeforeStart >= this.timeRand) {
          this.fishes = 1
          this.scoreTwo = this.scoreTwo + 10
          this.timeBeforeStart = 0
          this.gif2 = 'narik-p2.gif'
          this.gif = 'idle-p1.gif'
          setTimeout(() => {
            this.fishes = 0
            this.gif2 = 'idle-p2.gif'
          }, 1500)
        }
        let obj = {
                score: this.scoreTwo,
                role: this.userRole
            }
          this.$socket.emit('narikSocket', obj)
      }
    },
    randomTime () {
      this.timeRand = Math.floor(Math.random() * (50 - 30 + 1)) + 30
    },
    gantiKeIdle () {
      this.gif = 'idle-p1.gif'
    },
    update () {
      if (this.timeBeforeStart > this.timeRand) {
        this.gif = 'umpan-dimakan-p1.gif'
        this.gif2 = 'umpan-dimakan-p2.gif'
      } else {
        this.timeBeforeStart++
      }
    },
    interval () {
      console.log(this.cekInterval, 'cek Interval')
      setInterval(this.update, this.cekInterval)
    },
    start () {
      this.interval()
    }
  },
  created () {
    this.interval()
    this.randomTime()
  },
  sockets: {
    scorePlayer1(score){
      console.log(score, "<<< score");
      this.scoreOne = score
    },
    scorePlayer2(score) {
      this.scoreTwo = score
    }
  },
  computed: {
    userRole () {
      return localStorage.role
    },
    dataUserPlayer1 () {
      return this.$store.state.player1.role
    },
    dataUserPlayer2 () {
      return this.$store.state.player2.role
    }
  },
  mounted () {
    console.log(this.dataUserPlayer1, "<<< mounted data player1");
  }
}
</script>

<style>
    #btn {
      position: absolute;
    }
    .flex-container {
      display: flex;
      justify-content: space-around;
      height: 550px;
    }

    #game {
    margin: 0 auto;
    width: 700px;
    height: 400px;
    background-image: url("../sprites/background.gif");
    background-size: 700px 400px;
    background-repeat: no-repeat;
    border: 5px solid grey;
    border-radius: 15px;
    }
    #player {
    top: -4rem;
    left: 3rem;
    position: relative;
    display: inline;
    }

    #player img {
      height: 550px;
    }

    #playertwo {
    top: -5.5rem;
    right: 2.2rem;
    position: relative;
    display: inline;
    }

    #playertwo img {
      height: 550px;
    }
    .wrapper{
      display: flex;
      justify-content: center;
    }
</style>
