<template>
  <div>
    <option @click="getColors()" :style="bgColor">{{theme.label}}</option>
  </div>
</template>

<script>
import * as chroma from 'chroma-js';

export default {
	props: ['theme'],
  data() {
    return {
      bgColor: ''
    }
  },
	methods: {
		getColors() {
      const colors = chroma.scale([this.theme.colorFrom,this.theme.colorTo]).mode('lch').colors(7);
      this.$emit('changeColors',colors);
		},
	},
  mounted() {
    const colors = chroma.scale([this.theme.colorFrom,this.theme.colorTo]).mode('lch').colors(7);
    this.bgColor = "background: linear-gradient(90deg, " + colors[0] + " 0%, " + colors[colors.length - 1] + " 100%";
console.log(this.bgColor)
  }
}
</script>

<style>

</style>