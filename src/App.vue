<template>
  <div id="app">
    <div class="board">
      <div class="letter" v-for="n in amountOfFlaps" :key="n">
        <div ref="a1" class="top">{{topArray[n-1]}}</div>
        <div ref="a2" class="bottom" :style="{ animationDuration: `${speed}s` }">{{bottomArray[n-1]}}</div>
        <div ref="b2" class="nextHalf" :style="{ animationDuration: `${speed}s` }">{{nextHalfArray[n-1]}}</div>
        <div ref="b1" class="nextFull">{{nextFullArray[n-1]}}</div>
      </div>
    </div>
  </div>
</template>

<script>
import socketIo from 'socket.io-client'
import axios from 'axios'

export default {
  name: 'app',
  data () {
    return {
      audio: null,
      audiofiles: [
        'split-flap-1.wav',
        'split-flap-2.wav',
        'split-flap-3.wav',
        'split-flap-4.wav',
        'split-flap-5.wav',
        'split-flap-6.wav',
        'split-flap-7.wav',
        'split-flap-8.wav',
        'split-flap-9.wav',
        'split-flap-10.wav'
      ],
      currentString: 0,
      messageStrings: [],
      topArray: [],
      bottomArray: [],
      nextFullArray: [],
      nextHalfArray: [],
      speed: 0.075,
      amountOfFlaps: 160,
      beginStr: ''.toUpperCase().split(''),
      endStr: ''.toUpperCase().split(''),
      char: ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L',
        'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z',
        '1', '2', '3', '4', '5', '6', '7', '8', '9', '0',
        ' ', ',', '.', '"', ':', '-', '(', ')', '/'],
      strCount: [],
      flag: [],
      flag2: false,
      bottomFlip: false,
      nextHalfFlip: false,
      interval: null,
      streamlabsToken: '',
      streamlabsApiToken: '',
      streamlabsConnection: null,
      streamlabels: null,
      messageFileData: [],
      time_between_flaps: 30000
    }
  },
  methods: {
    flipIt (x) {
      this.topArray.splice(x, 1, this.char[(this.strCount[x] === 0) ? this.char.length - 1 : this.strCount[x] - 1])
      this.bottomArray.splice(x, 1, this.char[(this.strCount[x] === 0) ? this.char.length - 1 : this.strCount[x] - 1])
      this.nextFullArray.splice(x, 1, this.char[this.strCount[x]])
      this.nextHalfArray.splice(x, 1, this.char[this.strCount[x]])

      this.$refs.a2[x].classList.remove('flip1')
      this.$refs.a2[x].classList.add('flip1')
      this.$refs.b2[x].classList.remove('flip2')
      this.$refs.b2[x].classList.add('flip2')

      if (this.strCount[x] > this.char.length - 2) this.strCount[x] = 0
      else this.strCount[x]++
    },
    dontFlipIt (x) {
      this.flag[x] = true
      this.topArray.splice(x, 1, this.char[(this.strCount[x] === 0) ? this.char.length - 1 : this.strCount[x] - 1])
      this.bottomArray.splice(x, 1, this.char[(this.strCount[x] === 0) ? this.char.length - 1 : this.strCount[x] - 1])
    },
    changeDestination () {
      setTimeout(() => {
        this.audio = new Audio(this.audiofiles[Math.floor(Math.random() * 10)])
        this.audio.loop = true
        this.audio.volume = 0.005
        let playPromise = this.audio.play()
        if (playPromise !== undefined) {
          playPromise
            .then(_ => {
              // Automatic playback started!
              // Show playing UI.
            })
            .catch(_ => {
              // Auto-play was prevented
              // Show paused UI.
            })
        }

        this.flag.fill(false)
        this.flag2 = true
        this.currentString += 1
        if (this.currentString >= this.messageStrings.length) {
          this.currentString = 0
        }
        this.endStr = this.messageStrings[this.currentString]
      }, this.time_between_flaps)
    },
    setMessages () {
      this.messageStrings = []
      this.messageFileData.forEach(message => {
        // eslint-disable-next-line
        this.messageStrings.push(es6template(message, {
          streamlabels: this.streamlabels
        }).toUpperCase().split(''))
      })
      this.messageStrings.forEach((item, index) => {
        if (item.length >= 41 && item[39] !== ' ' && item[40] !== ' ') {
          // find last space index where index < 41
          let spaceIndex = item.slice(0, 40).lastIndexOf(' ')
          for (let addspace = 0; addspace < 39 - spaceIndex; addspace++) {
            // pad item from index to 40
            item.splice(spaceIndex, 0, ' ')
          }
        }
        if (item.length >= 81 && item[79] !== ' ' && item[80] !== ' ') {
          // find last space index where index < 41
          let spaceIndex = item.slice(0, 80).lastIndexOf(' ')
          for (let addspace = 0; addspace < 79 - spaceIndex; addspace++) {
            // pad item from index to 40
            item.splice(spaceIndex, 0, ' ')
          }
        }
        if (item.length >= 121 && item[119] !== ' ' && item[120] !== ' ') {
          // find last space index where index < 41
          let spaceIndex = item.slice(0, 120).lastIndexOf(' ')
          for (let addspace = 0; addspace < 119 - spaceIndex; addspace++) {
            // pad item from index to 40
            item.splice(spaceIndex, 0, ' ')
          }
        }
        if (item.length !== this.amountOfFlaps) {
          let toAdd = this.amountOfFlaps - item.length
          for (let y = 0; y < toAdd; y++) {
            item.push(' ')
          }
        }
        this.messageStrings[index] = item
      })
    },
    getData () {
      var link = 'data.json'
      var linkLocal = 'data.json.local'
      return axios
        .get(linkLocal)
        .then(
          response => response,
          error => {
            console.error(error)
            return axios.get(link)
          }
        )
        .then((response) => {
          let messages = response.data
          this.messageFileData = messages.strings
          this.streamlabsToken = messages.streamlabs.socket_token
          this.streamlabsApiToken = messages.streamlabs.api_token
          this.time_between_flaps = messages.time_between_flaps * 1000
          return true
        })
    }
  },
  mounted () {
    this.getData()
      .then(_ => {
        this.streamlabsConnection = socketIo(`https://sockets.streamlabs.com?token=${this.streamlabsToken}`, { transports: ['websocket'] })
        this.streamlabsConnection.on('event', (eventData) => {
          if (eventData.type === 'streamlabels') {
            this.streamlabels = eventData.message.data
            this.setMessages()
          }
        })

        axios.get(`https://streamlabs.com/api/v5/stream-labels/files?token=${this.streamlabsApiToken}`)
          .then((response) => {
            this.streamlabels = response.data.data
            this.setMessages()

            this.beginStr = this.messageStrings[0]
            this.endStr = this.messageStrings[1]

            for (var a = 0; a < this.amountOfFlaps; a++) {
              this.topArray.push('')
              this.bottomArray.push('')
              this.nextFullArray.push('')
              this.nextHalfArray.push('')
            }

            for (var z = 0; z < this.amountOfFlaps; z++) {
              this.strCount[z] = this.char.indexOf(this.beginStr[z])
              this.flag[z] = false
              this.flag2 = true
            }

            this.interval = setInterval(() => {
              for (var x = 0; x < this.amountOfFlaps; x++) {
                if (this.nextFullArray[x] === this.endStr[x]) this.dontFlipIt(x)
                else this.flipIt(x)

                if (this.flag.every((e) => e) && this.flag2) {
                  this.flag2 = false
                  if (this.audio !== null) {
                    this.audio.pause()
                  }
                  this.changeDestination()
                }
              }
            }, this.speed * 1000)
          })
      })
  }
}
</script>

<style>
body {
  background-color: transparent;
  padding:0;
  margin:0;
}
.board {
  width: 1200px;
  padding: 0;
  margin: 0;
  list-style: none;
  background-color: #222;
  padding-bottom:5px;
  padding-left:5px;
  -ms-box-orient: horizontal;
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -moz-flex;
  display: -webkit-flex;
  display: flex;
  -webkit-flex-wrap: wrap;
  flex-wrap: wrap;
}
.letter {
  padding: 0px;
  padding-right:5px;
  padding-top:5px;
  width: 25px;
  height: 25px;
  margin: 0px;

  line-height: 25px;
  font-size: 25px;
  color: white;

  text-align: center;
  font-family: Monospace;
  text-align: center;
  color: white;

}

.splitflap {
  min-width: 25px;
  height: 25px;
  margin-right: 5px;
  line-height: 25px;
  font-size: 25px;
  font-family: Monospace;
  text-align: center;
  color: white;
}

.center {
  position: relative;
    margin-top: 5px;
    display: flex;
    -webkit-box-pack: center;
    -ms-flex-pack: center;
    justify-content: center;
}

.top {
  position: relative;
  height: 50%;
  width: 100%;
  background-color: #000;
  border-radius: 5px 5px 0 0;
  overflow: hidden;
  z-index: 0;
}

div {
  perspective: 500px;
}

.bottom {
  position: relative;
  height: 100%;
  width: 100%;
  margin-top: -50%;
  border-radius: 5px 5px 5px 5px;
  z-index: -1;
  background-color: black;
  background-image: linear-gradient(rgba(0, 0, 0, 0), #000);
  transform-origin: center;
}

.nextHalf {
  position: relative;
  height: 50%;
  width: 100%;
  margin-top: -100%;
  overflow: hidden;
  border-radius: 5px 5px 0 0;
  z-index: 2;
  background-color: black;
  background-image: linear-gradient(#000, rgba(0, 0, 0, 0));
  transform-origin: bottom;
}

.nextFull {
  position: relative;
  height: 100%;
  width: 100%;
  background-color: #000;
  margin-top: -50%;
  border-radius: 10px 10px 10px 10px;
  z-index: -3;
}

.flip1 {
  animation: flip1 ease-in 1;
  animation-duration: 1s;
}

.flip2 {
  animation: flip2 ease-out 1;
  animation-duration: 1s;
}

@keyframes flip1 {
  0% {
    transform: rotateX(0deg);
    background-color: #000;
  }
  50% {
    transform: rotateX(90deg);
    background-color: black;
  }
  100% {
    transform: rotateX(90deg);
  }
}

@keyframes flip2 {
  0% {
    transform: rotateX(-90deg);
  }
  50% {
    transform: rotateX(-90deg);
  }
  100% {
    transform: rotateX(0deg);
    background-color: #000;
  }
}
</style>
