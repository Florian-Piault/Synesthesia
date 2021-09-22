<template>
  <div>
		<button @click="startAudio()" :style="this.couleur == ''? '' : {backgroundColor:this.couleur}">{{pitch}}</button>
  </div>
</template>

<script>
import * as Tone from 'tone';

export default {
	props: ['pitch', 'effect','couleur'],
	data(){
		return {
			style: '{background-color:white}'
		}
	},
	// EFFECTS : none / reverb / chorus / bitCrusher
	methods: {
		startAudio() {
			// NONE
			if (this.effect === 'none') {
				const synth = new Tone.FMSynth().toDestination();
				synth.triggerAttackRelease(this.pitch, "8n");
			}

			// BIT CRUSHER
			if (this.effect === 'bitCrusher') {
				const crusher = new Tone.BitCrusher(4).toDestination();
				const synth = new Tone.Synth().connect(crusher);
				synth.triggerAttackRelease(this.pitch, 1);
			}

			// CHORUS
			if (this.effect === 'chorus') {
				const chorus = new Tone.Chorus(4, 2.5, 0.5).toDestination().start();
				const synth = new Tone.PolySynth().connect(chorus);
				synth.triggerAttackRelease([this.pitch, this.pitch, this.pitch], "8n");
			}
			
			Tone.start();
		}
	},

}
</script>

<style>
	button.notes {
		height:100px;
		width: 100px;
		margin: 0;
		padding: 0;
	}
</style>