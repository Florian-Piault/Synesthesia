<template>
  <div>
		<button @click="startAudio()">{{pitch}}</button>
  </div>
</template>

<script>
import * as Tone from 'tone';

export default {
	props: ['pitch', 'effect'],
	// EFFECTS : none / reverb / chorus / bitCrusher
	methods: {
		startAudio() {
			const crusher = new Tone.BitCrusher(4).toDestination();
			const synth = new Tone.Synth().connect(crusher);
			synth.triggerAttackRelease(this.pitch, 2);
			Tone.start();
			// this.synth.triggerAttackRelease(this.pitch, "8n");
			// Tone.start();
		}
	}



}
</script>

<style>
	button {
		height:100px;
		width: 100px;
		margin: 0;
		padding: 0;
	}
</style>