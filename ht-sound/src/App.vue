<template>
  <main>
    <div class="mt-5 container">
      <div class="header">menu</div>
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
      <div class="play flex-center">
        <button @click="playSound()">Play</button>
      </div>
      <div class="notes flex-center">
        <div>
          <h4 class="mb-3">Notes</h4>
          <draggable
            class="draggable-list"
            :list="availableNotes"
            :group="{ name: 'myGroup', pull: 'clone', put: false }"
          >
            <div
              class="list-item"
              v-for="element in availableNotes"
              :key="element.name"
            >
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
    </div>
    <Visual v-if="truc" />
  </main>
</template>
<script>
import draggable from "vuedraggable";
import * as Tone from "tone";
import Visual from "./components/Visual.vue";
import { fireworks } from "./assets/js/fireworks";
// import { circle } from "./assets/js/circle";

export default {
  components: {
    draggable,
    Visual
  },
  data() {
    return {
      truc: false,
      sheet: [],
      trash: [],
      availableNotes: [
        { name: "A" },
        { name: "B" },
        { name: "C" },
        { name: "D" },
        { name: "E" },
        { name: "F" },
        { name: "G" }
      ]
    };
  },
  methods: {
    playSound() {
      this.truc = true;
      let synth;
      // const now = Tone.now();
      // let effect;
      // const seq = ["E4", "D#4", "E4", "D#4", "E4", "B3", "D4", "C4", "A3"];
      // INSTRUMENT
      // if (this.instrument === 'fmSynth') synth = new Tone.FMSynth();
      // else if (this.instrument === 'amSynth') synth = new Tone.AMSynth();
      // else if (this.instrument === 'synth') synth = new Tone.Synth();
      synth = new Tone.Synth().toDestination();

      // EFFET
      // if (this.effect !== 'none'){
      //   if (this.effect === 'distortion') effect = new Tone.Distortion(1).toDestination();
      //   if (this.effect === 'bitCrusher') effect = new Tone.BitCrusher(3).toDestination();
      //   if (this.effect === 'chorus') effect = new Tone.Chorus(4, 2.5, 0.5).toDestination().start();
      //   if (this.effect === 'chebyshev') effect = new Tone.Chebyshev(50).toDestination();

      //   synth = synth.connect(effect);
      // }
      // else synth = synth.toDestination();

      // this.music.forEach((note, i) => {
      //   synth.triggerAttackRelease(note + this.Height, "8n", now + i);
      // })
      new Tone.Sequence((time, note) => {
        synth.triggerAttackRelease(note.name + "4", 0.1, time);
        fireworks();
        // circle();
      }, this.sheet).start(0);
      Tone.Transport.start();

      // const analyser = new Tone.Analyser();
      // console.log(analyser);
    }
  }
};
</script>
<style scoped>
@import url("./assets/css/style.css");
</style>
