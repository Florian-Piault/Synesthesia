<template>
  <main>
    <div class="mt-5 container">
      <div class="header">
        <!-- EFFET -->
        <label>Effet:</label>
        <select v-model="activeEffect">
          <option v-for="e in effects" :value="e" :key="e"> {{e}}</option>
        </select>
        <!-- INSTRUMENT -->
        <label>Instrument:</label>
        <select v-model="activeInstrument">
          <option v-for="i in instruments" :value="i" :key="i"> {{i}}</option>
        </select>
      </div>

      <!-- PARTITION -->
      <div class="music">
        <div>
          <h4 class="mb-3">Musique</h4>
          <draggable
            class="draggable-list"
            :list="sheet"
            :group="{ name: 'myGroup', put: true }"
          >
            <div class="list-item" v-for="element in sheet" :key="element.name">
              {{ element.name }}
            </div>
          </draggable>
        </div>
      </div>

      <!-- BUTTONS -->
      <div class="play flex-center">
        <button @click="playSound()">Play</button>
        <button @click="stopSound()">Stop</button>
      </div>

      <!-- NOTES DISPONILBES -->
      <div class="notes flex-center">
        <div>
          <h4 class="mb-3">Notes</h4>
          <draggable
            class="draggable-list"
            :list="availableNotes"
            :group="{ name: 'myGroup', pull: 'clone', put: false }"
          >
            <div class="list-item" v-for="element in availableNotes" :key="element.name">
              {{ element.name }}
            </div>
          </draggable>
        </div>

        <div>
          <h4 class="mb-3">Poubelle</h4>
          <draggable
            class="draggable-list trash"
            :list="trash"
            :group="{ name: 'myGroup', pull: 'clone', put: true }"
          >
          </draggable>
        </div>
      </div>
    <!-- <div class="notes-panel">
      <Note v-for="(note,i) in notes" :pitch="note + Height" :effect="effect" :key="note" :couleur='colors.length==0 ? "" : colors[i]'></Note>  
    </div>

    <label for="">Choisir base de couleurs pour la palette</label>
    <div class="colorsPanel">
      <Color v-for="color in themes" :color="color" @changeColors='changeColors($event)' :key="color"></Color>
    </div> -->
  </main>
</template>
<script>
import draggable from 'vuedraggable';
import * as Tone from 'tone';
import Note from './components/Note.vue';
import Color from './components/Color.vue';

export default {
  components: {
    draggable,
    Note,
    Color
  },
  data() {
    return {
      sheet: [],
      trash: [],
      availableNotes: [
        { name: "C" },
        { name: "D" },
        { name: "E" },
        { name: "F" },
        { name: "G" },
        { name: "A" },
        { name: "B" },
      ],
      effects: ["distortion", "bitCrusher","chorus", "chebyshev", "none"],
      activeEffect: "chebyshev",
      instruments: ["fmSynth", "amSynth", "synth"],
      activeInstrument: "synth",
      activeTheme: 'green',
      themes: ['green', 'pink', 'brown', 'blue', 'blueviolet', 'white', 'dark'],
      gradient: [],
    }
  },
  methods: {
    playSound() {
      let synth;
      let effect;
      // const now = Tone.now();
      // const seq = ["E4", "D#4", "E4", "D#4", "E4", "B3", "D4", "C4", "A3"];
      // INSTRUMENT 
      // if (this.instrument === 'fmSynth') synth = new Tone.FMSynth();
      // else if (this.instrument === 'amSynth') synth = new Tone.AMSynth();
      // else if (this.instrument === 'synth') synth = new Tone.Synth();
      synth = new Tone.Synth().toDestination(); 

      // EFFET
      if (this.activeEffect !== 'none'){
        if (this.activeEffect === 'distortion') effect = new Tone.Distortion(1).toDestination();
        if (this.activeEffect === 'bitCrusher') effect = new Tone.BitCrusher(3).toDestination();
        if (this.activeEffect === 'chorus') effect = new Tone.Chorus(4, 2.5, 0.5).toDestination().start();
        if (this.activeEffect === 'chebyshev') effect = new Tone.Chebyshev(50).toDestination();

        synth = synth.connect(effect);
      }
      else synth = synth.toDestination();

      new Tone.Sequence((time, note) => {
        synth.triggerAttackRelease(note.name + '4', 0.1, time);
      }, this.sheet).start(0);
      Tone.Transport.start();
    
      // const analyser = new Tone.Analyser();
      // console.log(analyser);
    },
    stopSound() {
      Tone.Transport.stop();
    },
    changeColors(colors){
      this.colors = colors;
      console.log(colors);
    }
  }
}

</script>
<style scoped>
  @import url("./assets/css/style.css");
</style>
