<template>
  <div id="app">
    <input type="number" v-on:input="onInput" :value="val" />
    <Ruler ref="refSlideValue" @onchange="onBsChange" />
  </div>
</template>

<script>
import Ruler from './components/vue-ruler'

export default {
  name: 'App',
  data () {
    return {
      range: [0, 300],
      val: 0
    };
  },
  components: {
    Ruler
  },
  methods: {
    onInput (e) {
      const val = e.target.value;
      const idx = val / 0.1;
      const min = this.range[0];
      const max = this.range[1];
      const limitVal = idx < min ? min : (idx > max ? max : idx);
      this.$refs.refSlideValue.doOnValueChange(limitVal);
    },
    onBsChange(index) {
      this.val = (0.1 * index).toFixed(1);
    }
  }
}
</script>

<style>
  body {
    margin: 0;
  }
  input {
    display: block;
    width: 150px;
    max-width: 630px;
    outline: none;
    border: none;
    font-size: 64px;
    border-bottom: 2px solid #5199f7;
    background-color: transparent;
    color: #5199f7;
    text-align: center;
    margin: 0 auto;
    margin-bottom: 40px;
  }
</style>
