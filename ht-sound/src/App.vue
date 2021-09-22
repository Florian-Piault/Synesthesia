<template>
  <main :style="{ 'background-image': 'url(BG_' + gradient_BG.id + '.jpg)' }">
    <div class="mt-5 container">
      <div class="header">
        <!-- open modale  -->
        <div v-if="!isModaleOpen" class="btn-container">
          <button class="open-modale" @click="openModale()">
            <img src="@/assets/menu.svg" alt="close" class="open-modale" />
          </button>
        </div>

        <!-- modale -->
        <transition appear mode="in-out" name="slide">
          <Modale v-if="isModaleOpen" @closeModale="closeModale()">
            <h2>Chose your palette of colors</h2>
            <Color
              v-for="(theme, index) in themes"
              :theme="theme"
              @changeColors="changeColors($event, index)"
              :key="theme.color"
            ></Color>
          </Modale>
        </transition>
      </div>

      <!-- PARTITION -->
      <div class="music column">
        <h1>Compose your colored combination</h1>
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
      sequence: null,
      loopNumber: 1,
      tempo: 0.5,
      sheet: [
        { name: "C4", color: "#ee74e1" },
        { name: "C4", color: "#ee74e1" },
        { name: "C4", color: "#ee74e1" },
        { name: "D4", color: "#b197ff" },
        { name: "E4", color: "#2bb6ff" },
        { name: "D4", color: "#b197ff" },
        { name: "C4", color: "#ee74e1" },
        { name: "E4", color: "#2bb6ff" },
        { name: "D4", color: "#b197ff" },
        { name: "D4", color: "#b197ff" },
        { name: "C4", color: "#ee74e1" },
      ],
      trash: [],
      availableNotes: [
        { name: "C4", color: "#C19EE0" },
        { name: "D4", color: "#B185DB" },
        { name: "E4", color: "#A06CD5" },
        { name: "F4", color: "#9163CB" },
        { name: "G4", color: "#815AC0" },
        { name: "A4", color: "#7251B5" },
        { name: "B4", color: "#6247AA" },
      ],
      effects: ["distortion", "bitCrusher", "chorus", "chebyshev", "none"],
      activeEffect: "none",
      instruments: ["fmSynth", "amSynth", "synth"],
      activeInstrument: "synth",
      activeTheme: "green",
      themes: [
        { colorFrom: "#C19EE0", colorTo: "#6247AA", label: "Default" },
        { colorFrom: "#e89be0", colorTo: "#82f7cc", label: "Rainbow ☆" },
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
      Tone.Transport.clear();
      if (this.sequence) this.sequence.dispose();

      let effect;
      if (this.gradient_BG.id !== 0) {
        if (this.gradient_BG.id === 1) {
          this.synth = new Tone.Synth();
          effect = new Tone.Distortion(1).toDestination();
        }
        if (this.gradient_BG.id === 2) {
          this.synth = new Tone.Synth();
          effect = new Tone.BitCrusher(3).toDestination();
        }
        if (this.gradient_BG.id === 3) {
          this.synth = new Tone.Synth();
          effect = new Tone.Chorus(4, 2.5, 0.5).toDestination().start();
        }
        if (this.gradient_BG.id === 4) {
          this.synth = new Tone.FMSynth();
          effect = new Tone.Chebyshev(50).toDestination();
        }

        this.synth = this.synth.connect(effect);
      } else {
        this.synth = new Tone.Synth();
        this.synth = this.synth.toDestination();
      }

      this.sequence = new Tone.Sequence((time, note) => {
        this.synth.triggerAttackRelease(note.name, "8t", time);
      }, this.sheet);

      this.sequence.loop = this.loopNumber;
      this.sequence.playbackRate = 1;
      this.sequence.start(0);
      Tone.Transport.start();
    },
    stopSound() {
      Tone.Transport.stop();
      Tone.Transport.clear();
      this.sequence.dispose();
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
.btn-container {
  position: absolute;
}
.open-modale {
  cursor: pointer;
}

.slide-enter-active,
.slide-leave-active {
  transition: 0.5s ease-in-out;
  left: 0;
}

.slide-enter,
.slide-leave-to {
  left: -512px;
}
@import url("./assets/css/style.css");
</style>
