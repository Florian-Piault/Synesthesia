<template>
  <div id="app">
    <h1>TONE.JS</h1>
    <button @click="startAudio()"> Start </button>
  </div>
</template>

<script>
import * as Tone from 'tone';

export default {
  data(){
    return {
      synth: null
    }
  },
  name: 'App',
  components: {
  },
  mounted(){
    this.synth = new Tone.Synth().toDestination();
  },
  methods: {
    startAudio(){
          this.synth.triggerAttackRelease("C1", "8n");

      Tone.start();
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
  color: #2c3e50;
  margin-top: 60px;
}
</style>
