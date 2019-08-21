<template>
  <div id="app">
    <h5>{{ displayTime }}</h5>
    <p>{{ randMult }}</p>
    <input type="number" v-model="input">
    <button @click="start">Start</button>

    <div v-if="randMult === 'DONE'">
      <h2>Well Done!</h2>
      <p>Your time: {{ endTime }}</p>
      <h4>Last 5 rounds:</h4>
      <p v-for="entry, i in history" :key="i">{{ entry.date }}: {{ entry.time }}s</p>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from 'vue-property-decorator';

interface HistoryEntry {
  date:string,
  time:number,
}
@Component
export default class App extends Vue {
  numbers: string[][] = [['']]

  randMult: string = 'x * y';

  input: string = '';

  target: number = -1;

  startTime: number = -1;

  displayTime: number = 0;

  endTime: number = 0;

  intervalID: number = -1;

  history:HistoryEntry[] = []

  @Watch('input')
  onInput() {
    if (this.isCorrect && this.startTime !== -1) {
      this.logic();
    }
  }

  start() {
    this.numbers = App.genArray();
    this.startTime = Date.now();
    this.intervalID = window.setInterval(() => {
      this.displayTime = Math.floor((Date.now() - this.startTime) / 1000);
    }, 100);
    this.logic(); // Trigger a start
  }

  logic() {
    this.randMult = this.getRandMult();
    if (this.randMult === 'DONE') {
      // Finish
      window.clearInterval(this.intervalID);
      this.endTime = (Date.now() - this.startTime) / 1000;
      this.startTime = -1;

      if (this.history.length === 5) this.history.shift();
      this.history.push({ date: new Date().toLocaleString(), time: this.endTime });
    } else {
      this.input = '';
      const term1 = Number(this.randMult.substr(0, 2));
      const term2 = Number(this.randMult.substr(-2, 2)); // Use a length of 2 to correctly parse 10
      this.target = term1 * term2;
    }
  }

  get isCorrect(): boolean {
    return Number(this.input) === this.target;
  }

  // Get a random number from the array. Array will update in a immutable manner.
  getRandMult(): string {
    const i1 = Math.floor(Math.random() * this.numbers.length);
    if (this.numbers[i1] === undefined) return 'DONE';

    const i2 = Math.floor(Math.random() * this.numbers[i1].length);
    const ret = this.numbers[i1];

    // Remove entry if it will be made empty.
    if (ret.length === 1) {
      const newArr = this.numbers.filter(v => v !== ret); // Sexy???
      this.numbers = newArr;
    } else {
      const newArr = ret.filter(v => v !== ret[i2]);
      this.$set(this.numbers, i1, newArr); // Make Vue reactive.
    }
    return ret[i2];
  }

  static genArray(): string[][] {
    return Array.from({ length: 10 }, (
      (value, ind) => Array.from({ length: 10 }, (k, i) => `${i + 1} * ${ind + 1}`)
    ));
  }

  mounted() {
    this.numbers = App.genArray();
  }
}
</script>
<style lang="stylus">
#app
  font-family 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing antialiased
  -moz-osx-font-smoothing grayscale
  text-align center
  color #2c3e50
  margin-top 60px
</style>
