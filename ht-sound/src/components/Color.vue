<template>
  <div>
          <option @click="getColors()" class="colors">{{color}}</option>
  </div>
</template>

<script>
import * as chroma from 'chroma-js';

export default {
	props: ['color'],
    
	methods: {
		getColors() {
            console.log(this.color)
            let hexa = chroma(this.color).darken().saturate(2).hex()
            const colors = chroma.scale(['#fafa6e',hexa]).mode('lch').colors(7)
            console.log(colors)
            this.$emit('changeColors',colors)
            colors;
		},

        transformNotes(){
            console.log("coucou")
        }       
	}
}
</script>

<style>

</style>