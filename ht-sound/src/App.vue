<template>
  <main :style="{ 'background-image': 'url(BG_' + gradient_BG.id + '.jpg)' }">
    <div class="wrap_animation"><canvas class="fireworks"></canvas></div>
    <div class="mt-5 container">
      <div class="header">
        <!-- open modale  -->
        <div
          v-if="!isModaleOpen"
          class="btn-container d-flex justify-align-center"
        >
          <button class="open-modale button-modale" @click="openModale()">
            <img src="@/assets/menu.svg" alt="close" class="open-modale" />
          </button>

          <p>⇽ Select your mood</p>
        </div>

        <!-- modale -->
        <transition appear mode="in-out" name="slide">
          <Modale v-if="isModaleOpen" @closeModale="closeModale()">
            <h2>Chose your palette mood :</h2>
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
        <h1>Compose your colored partition</h1>

        <p class="secondary">Drag & Drop Notes to the following partition ↴</p>
        <div>
          <draggable
            class="draggable-list"
            :list="sheet"
            :group="{ name: 'myGroup', put: true }"
          >
            <div
              @dblclick="removeFromSheet(index)"
              class="list-item"
              :style="
                element.color !== '' ? 'background-color:' + element.color : ''
              "
              v-for="(element, index) in sheet"
              :key="element.name"
              :id="'index_' + index"
            >
              {{ element.label }}
            </div>
          </draggable>
        </div>
        <p class="theme_label">Mood: {{ gradient_BG.label }}</p>
      </div>

      <!-- BUTTONS -->
      <div class="play flex-center">
        <button @click="playSound()">Discover your song</button>
        <!-- <button @click="stopSound()">Re compose</button> -->
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
              @dblclick="addToSheet(element)"
              class="list-item"
              :style="
                element.color !== '' ? 'background-color:' + element.color : ''
              "
              v-for="element in availableNotes"
              :key="element.name"
            >
              {{ element.label }}
            </div>
          </draggable>
        </div>

        <div class="trash" @click="cleanAll()">
          <draggable
            class="draggable-list trashList"
            :list="trash"
            :group="{ name: 'myGroup', pull: 'clone', put: true }"
          >
          </draggable>
        </div>
        ← The trash is HERE
      </div>
    </div>
  </main>
</template>
<script>
import draggable from "vuedraggable";
import Color from "./components/Color.vue";
import Modale from "./components/Modale.vue";
import * as Tone from "tone";
import { fireworks } from "./assets/js/fireworks.js";

export default {
  components: {
    draggable,
    Color,
    Modale,
  },
  watch: {
    sheet: function (newValue) {
      newValue.forEach((element, index) => {
        element.id = "index_" + index;
      });
      console.log();
    },
  },
  data() {
    return {
      synth: null,
      sequence: null,
      loopNumber: 1,
      tempo: 0.5,
      sheet: [
        { name: "C4", label: "DO", color: "#ee74e1", id: "index_0" },
        { name: "C4", label: "DO", color: "#ee74e1", id: "index_1" },
        { name: "C4", label: "DO", color: "#ee74e1", id: "index_2" },
        { name: "D4", label: "RE", color: "#b197ff", id: "index_3" },
        { name: "E4", label: "MI", color: "#2bb6ff", id: "index_4" },
        { name: "D4", label: "RE", color: "#b197ff", id: "index_5" },
        { name: "C4", label: "DO", color: "#ee74e1", id: "index_6" },
        { name: "E4", label: "MI", color: "#2bb6ff", id: "index_7" },
        { name: "D4", label: "RE", color: "#b197ff", id: "index_8" },
        { name: "D4", label: "RE", color: "#b197ff", id: "index_9" },
        { name: "C4", label: "DO", color: "#ee74e1", id: "index_10" },
      ],
      trash: [],
      availableNotes: [
        { name: "C4", label: "DO", color: "#C19EE0" },
        { name: "D4", label: "RE", color: "#B185DB" },
        { name: "E4", label: "MI", color: "#A06CD5" },
        { name: "F4", label: "FA", color: "#9163CB" },
        { name: "G4", label: "SOL", color: "#815AC0" },
        { name: "A4", label: "LA", color: "#7251B5" },
        { name: "B4", label: "SI", color: "#6247AA" },
      ],
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
      gradient_BG: { id: 0, label: "Default" },
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
      let i = 0;
      this.sequence = new Tone.Sequence((time, note) => {
        this.synth.triggerAttackRelease(note.name, "8t", time);
        if (this.sheet.length >= i) {
          fireworks(this.gradient, i);
          i++;
        } else {
          i = 0;
        }
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
    cleanAll() {
      this.sheet = [];
    },
    addToSheet(elt) {
      this.sheet.push(elt);
    },
    removeFromSheet(i) {
      this.sheet.splice(i, 1);
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
  .wrap_animation {
    overflow: hidden;
    position: absolute;
    pointer-events: none;
    width: 100%;
    height: 100%;
    z-index: 1000;
  }
  .btn-container {
    position: absolute;
  }
  .open-modale {
    cursor: pointer;
  }
  .trash {
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
