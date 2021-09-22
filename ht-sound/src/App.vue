<template>
  <main :style="{ 'background-image': 'url(BG_' + gradient_BG.id + '.jpg)' }">
    <div class="mt-5 container">
      <div class="header">
        <button @click="openModale()">open</button>
        <Modale v-if="isModaleOpen" @closeModale="closeModale()">
          <Color
            v-for="(theme, index) in themes"
            :theme="theme"
            @changeColors="changeColors($event, index)"
            :key="theme.color"
          ></Color>
        </Modale>
      </div>

      <!-- PARTITION -->
      <div class="music column">
        <h1>Compose your colored combinaison</h1>
        <div>
          <draggable
            class="draggable-list"
            :list="sheet"
            :group="{ name: 'myGroup', put: true }"
          >
            <div
              class="list-item"
              :style="
                element.color !== '' ? 'background-color:' + element.color : ''
              "
              v-for="element in sheet"
              :key="element.name"
            >
              {{ element.name }}
            </div>
          </draggable>
        </div>
        <p class="theme_label">
          {{ gradient_BG.label }}
        </p>
      </div>

      <!-- BUTTONS -->
      <div class="play flex-center">
        <button @click="playSound()">Discover your song</button>
        <button @click="stopSound()">Re compose</button>
      </div>

      <!-- NOTES DISPONILBES -->
      <div class="notes flex-center">
        <div>
          <draggable
            class="draggable-list"
            :list="availableNotes"
            :group="{ name: 'myGroup', pull: 'clone', put: false }"
          >
            <div
              class="list-item"
              :style="
                element.color !== '' ? 'background-color:' + element.color : ''
              "
              v-for="element in availableNotes"
              :key="element.name"
            >
              {{ element.name }}
            </div>
          </draggable>
        </div>

        <div class="trash">
          <draggable
            class="draggable-list trashList"
            :list="trash"
            :group="{ name: 'myGroup', pull: 'clone', put: true }"
          >
          </draggable>
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
        { name: "C", color: "#C19EE0" },
        { name: "D", color: "#B185DB" },
        { name: "E", color: "#A06CD5" },
        { name: "F", color: "#9163CB" },
        { name: "G", color: "#815AC0" },
        { name: "A", color: "#7251B5" },
        { name: "B", color: "#6247AA" },
      ],
      trash: [],
      availableNotes: [
        { name: "C", color: "#C19EE0" },
        { name: "D", color: "#B185DB" },
        { name: "E", color: "#A06CD5" },
        { name: "F", color: "#9163CB" },
        { name: "G", color: "#815AC0" },
        { name: "A", color: "#7251B5" },
        { name: "B", color: "#6247AA" },
      ],
      effects: ["distortion", "bitCrusher", "chorus", "chebyshev", "none"],
      activeEffect: "none",
      instruments: ["fmSynth", "amSynth", "synth"],
      activeInstrument: "synth",
      activeTheme: "green",
      themes: [
        { colorFrom: "#C19EE0", colorTo: "#6247AA", label: "Default" },
        { colorFrom: "#e89be0", colorTo: "#82f7cc", label: " Rainbow ☆" },
        { colorFrom: "#FBAB7E", colorTo: "#F7CE68", label: "Sunny ☀️" },
        { colorFrom: "#74EBD5", colorTo: "#9FACE6", label: "Rainy ☔" },

        { colorFrom: "#8EC5FC", colorTo: "#E0C3FC", label: "Cloudy ☁️" },
      ],
      gradient: [
        "#C19EE0",
        "#B185DB",
        "#A06CD5",
        "#9163CB",
        "#815AC0",
        "#7251B5",
        "#6247AA",
      ],
      gradient_BG: { id: 0, label: "Rainbow ☆" },
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
    changeColors(colors, index) {
      this.gradient_BG.id = index;
      this.gradient_BG.label = colors.label;
      this.gradient = colors.colors;
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
  computed: {
    bgButton() {
      return (
        "color: linear-gradient(0deg, " +
        this.gradient[0] +
        " 0%, " +
        this.gradient[this.gradient.length - 1] +
        " 100%)"
      );
    },
  },
};
</script>
<style scoped>
  @import url("./assets/css/style.css");
</style>
