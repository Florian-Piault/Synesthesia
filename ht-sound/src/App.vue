<template>
  <main>
    <div class="mt-5 container">
      <div class="header">
        <button @click="openModale()"> open </button>
        <Modale v-if="isModaleOpen" @closeModale="closeModale()">
          <Color v-for="theme in themes" :theme="theme" @changeColors='changeColors($event)' :key="theme.color"></Color>
        </Modale>
      </div>

      <!-- PARTITION -->
      <div class="music">
        <div>
          <draggable
            class="draggable-list"
            :list="sheet"
            :group="{ name: 'myGroup', put: true }"
          >
            <div class="list-item" :style=" element.color !== '' ? 'background-color:' + element.color : ''" v-for="element in sheet" :key="element.name">
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
            <div class="list-item" :style=" element.color !== '' ? 'background-color:' + element.color : ''" v-for="element in availableNotes" :key="element.name">
              {{ element.name }}
            </div>
          </draggable>
        </div>

          <div class="trash">
            <draggable
              class="draggable-list trashList"
              :list="list3"
              :group="{ name: 'myGroup', pull: 'clone', put: true }"
            >
            </draggable>
          </div>
        </div>
      </div>
    </div> 
  </main>
</template>
<script>
import draggable from "vuedraggable";
import Color from "./components/Color.vue";
import Modale from "./components/Modale.vue";
import * as Tone from "tone";

export default {
  components: {
    draggable,
    Color,
    Modale,
  },
  data() {
    return {
      synth: null,
      loopNumber: 1,
      tempo: 2,
      sheet: [
        { name: "C", color: "#ff6d93" },
        { name: "D", color: "#ef5b7a" },
        { name: "E", color: "#dd4a61" },
        { name: "F", color: "#cb3949" },
        { name: "G", color: "#b72832" },
        { name: "A", color: "#a3161b" },
        { name: "B", color: "#8e0000" },
      ],
      trash: [],
      availableNotes: [
        { name: "C", color: "#ff6d93" },
        { name: "D", color: "#ef5b7a" },
        { name: "E", color: "#dd4a61" },
        { name: "F", color: "#cb3949" },
        { name: "G", color: "#b72832" },
        { name: "A", color: "#a3161b" },
        { name: "B", color: "#8e0000" },
      ],
      effects: ["distortion", "bitCrusher", "chorus", "chebyshev", "none"],
      activeEffect: "none",
      instruments: ["fmSynth", "amSynth", "synth"],
      activeInstrument: "synth",
      activeTheme: "green",
      themes: [
        { colorFrom: "#85FFBD", colorTo: "#FFFB7D", label: "VERT" },
        { colorFrom: "#EE74E1", colorTo: "#3EECAC", label: "ROSE" },
        { colorFrom: "ff6d93", colorTo: "8e0000", label: "MARRON" },
        { colorFrom: "#9bc5c3", colorTo: "#6d66ca", label: "BLEU" },
        { colorFrom: "#fafa5e", colorTo: "#2A4858", label: "VIOLET-BLEU" },
      ],
      gradient: [
        "#ff6d93",
        "#ef5b7a",
        "#dd4a61",
        "#cb3949",
        "#b72832",
        "#a3161b",
        "#8e0000",
      ],
      isModaleOpen: false,
    };
  },
  methods: {
    playSound() {
      Tone.Transport.stop();
      let effect;
      // const now = Tone.now();
      // const seq = ["E4", "D#4", "E4", "D#4", "E4", "B3", "D4", "C4", "A3"];
      // INSTRUMENT
      // if (this.instrument === 'fmSynth') synth = new Tone.FMSynth();
      // else if (this.instrument === 'amSynth') synth = new Tone.AMSynth();
      // else if (this.instrument === 'synth') synth = new Tone.Synth();
      this.synth = new Tone.Synth();

      // EFFET
      if (this.activeEffect !== "none") {
        if (this.activeEffect === "distortion")
          effect = new Tone.Distortion(1).toDestination();
        if (this.activeEffect === "bitCrusher")
          effect = new Tone.BitCrusher(3).toDestination();
        if (this.activeEffect === "chorus")
          effect = new Tone.Chorus(4, 2.5, 0.5).toDestination().start();
        if (this.activeEffect === "chebyshev")
          effect = new Tone.Chebyshev(50).toDestination();

        this.synth = this.synth.connect(effect);
      } else this.synth = this.synth.toDestination();

      const seq = new Tone.Sequence((time, note) => {
        this.synth.triggerAttackRelease(note.name + "4", "8n", time);
      }, this.sheet);

      seq.loop = this.loopNumber;
      seq.playbackRate = this.tempo;
      seq.start(0);
      Tone.Transport.start();
    },
    stopSound() {
      Tone.Transport.stop();
    },
    changeColors(colors) {
      this.gradient = colors;
      this.availableNotes.forEach((note, i) => (note.color = this.gradient[i]));

      // change les notes déjà entrées
      this.availableNotes.map((exempleNote) => {
        this.sheet.filter((sheetNote) =>
          sheetNote.name === exempleNote.name
            ? (sheetNote.color = exempleNote.color)
            : null
        );
      });

      // ferme la modale
      this.closeModale();
    },
    closeModale() {
      this.isModaleOpen = false;
    },
    openModale() {
      this.isModaleOpen = true;
    },
  },
};
</script>
<style scoped>
  @import url("./assets/css/style.css");
</style>
