<template>
  <div id="app">
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

@Component
export default class App extends Vue {
  numbers: string[][] = [['']]

  getRandMult(): string {
    const i1 = Math.floor(Math.random() * this.numbers.length);
    const i2 = Math.floor(Math.random() * this.numbers[i1].length);
    const ret = this.numbers[i1][i2];

    if (ret.length === 1) {
      const cached = this.numbers[i1];
      const newArr = this.numbers.filter(v => v !== cached);
      this.numbers = newArr;
    } else {
      const newArr = this.numbers[i1].filter(v => v !== ret);
      this.$set(this.numbers, i1, newArr);
    }
    return ret;
  }

  mounted() {
    this.numbers = Array.from({ length: 10 }, (
      (value, ind) => Array.from({ length: 10 }, (k, i) => `${i + 1} * ${ind + 1}`)
    ));
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
