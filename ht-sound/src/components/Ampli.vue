<template>
  <div>
    <div class="gauge-container">
      <!-- NOTE -->
      <input type="range" class="gauge" min=261.6 max=493.9 step="0.1" list="frequency" v-model="range">
      <datalist id="frequency">
        <option v-for="f in frequencies" :key="f"> {{f}} </option>
      </datalist>
      <input type="number" class="gauge-value" v-model="range">
    </div>
    <!-- LONGUEUR -->
    <label>Longueur:</label>
    <select v-model="length">
      <option v-for="l in lengths" :value="l" :key="l"> {{l}}</option>
    </select> 
    <!-- INSTRUMENT -->
    <label>Instrument:</label>
    <select v-model="instrument">
      <option v-for="i in instruments" :value="i" :key="i"> {{i}}</option>
    </select>
    <!-- EFFET -->
    <label>Effect:</label>
    <select v-model="effect">
      <option v-for="e in effects" :value="e" :key="e"> {{e}}</option>
    </select>
    <!-- JOUER -->
    <button class="play" @click="playSound()">Jouer</button>
    <button class="play" @click="stopSound()">Stop</button>    
  </div>
</template>

<script>
import * as Tone from 'tone';

export default {
  data(){
    return {
      range: 440,
      length: "8n",
      instrument: "synth",
      effect: "none",
      frequencies: [261.6, 293.7, 329.6, 349.2, 392.0, 440, 493.9],
      lengths: ["2n", "4n", "8n", "16n"],
      instruments: ["fmSynth", "amSynth", "synth"],
      effects: ["distortion", "bitCrusher","chorus", "chebyshev", "none"]
    }
  },
  methods: {
    playSound() {
      let synth;
      let effect;
      const seq = ["E4", "D#4", "E4", "D#4", "E4", "B3", "D4", "C4", "A3"];

      // INSTRUMENT 
      if (this.instrument === 'fmSynth') synth = new Tone.FMSynth();
      else if (this.instrument === 'amSynth') synth = new Tone.AMSynth();
      else if (this.instrument === 'synth') synth = new Tone.Synth();

      // EFFET
      if (this.effect !== 'none'){
        if (this.effect === 'distortion') effect = new Tone.Distortion(1).toDestination();
        if (this.effect === 'bitCrusher') effect = new Tone.BitCrusher(3).toDestination();
        if (this.effect === 'chorus') effect = new Tone.Chorus(4, 2.5, 0.5).toDestination().start();
        if (this.effect === 'chebyshev') effect = new Tone.Chebyshev(50).toDestination();

        synth = synth.connect(effect);
      }
      else synth = synth.toDestination();
      
      new Tone.Sequence((time, note) => {
        synth.triggerAttackRelease(note, 0.1, time);
      }, seq).start(0);
      Tone.Transport.start();
    },
    stopSound() {
      Tone.Transport.stop();
    }
  }

}
</script>

<style>
  .gauge {
    width: 50%;
    margin-bottom: 16px;

  }

  .gauge-container {
    display: flex;
    flex-direction: row;
    justify-content: center;
  }

  .gauge-value {
    border: none;
  }
</style>