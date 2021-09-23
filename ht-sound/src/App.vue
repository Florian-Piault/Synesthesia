<template>
  <main :style="{ 'background-image': 'url(BG_' + gradient_BG.id + '.jpg)' }">
    <VueContext ref="ctxMenu">
      <ul class="ctx-menu" slot-scope="child">
        <li style="color: #2e3a59">OCTAVE :</li>
        <li
          v-for="o in octaves"
          @click="changeOctave(o, child.data)"
          :key="o"
          class="ctx-choice"
        >
          {{ o }}
        </li>
      </ul>
    </VueContext>
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

          <p>⇽ Select your mood & settings</p>
        </div>

        <!-- modale -->
        <transition appear mode="in-out" name="slide">
          <Modale
            v-if="isModaleOpen"
            @closeModale="closeModale()"
            :tempo="tempo"
          >
            <h2>Chose your palette mood ✵</h2>
            <Color
              v-for="(theme, index) in themes"
              :theme="theme"
              @changeColors="changeColors($event, index)"
              :key="theme.color"
            ></Color>
            <div class="d-flex check">
              <input v-model="displayNotes" type="checkbox" id="switch" /><label
                for="switch"
                class="switch"
                >Toggle</label
              >
              <p>Afficher les notes ♫</p>
            </div>
            <div class="d-flex check">
              <input v-model="loop" type="checkbox" id="switch2" /><label
                for="switch2"
                class="switch"
                >Toggle</label
              >
              <p>Loop ♾</p>
            </div>
            <div class="tempo">
              <input
                class="slider"
                type="range"
                v-model="tempo"
                name="tempo"
                step="0.1"
                min="0.1"
                max="3"
              />
              <label for="tempo">Tempo : </label>
              <p>{{ tempo }} ⌛</p>
            </div>
          </Modale>
        </transition>
      </div>

      <!-- PARTITION -->
      <div class="music column">
        <h1>Compose your colored combinaison ☹</h1>

        <p class="secondary">Drag & Drop shapes to the following container ↴</p>
        <div>
          <draggable
            class="draggable-list"
            :list="sheet"
            :group="{ name: 'myGroup', put: true }"
          >
            <div
              @contextmenu.prevent="$refs.ctxMenu.open($event, index)"
              @dblclick="removeFromSheet(index)"
              class="list-item"
              :style="
                element.color !== '' ? 'background-color:' + element.color : ''
              "
              v-for="(element, index) in sheet"
              :key="element.name"
              :id="'index_' + index"
            >
              <p>
                <template v-if="displayNotes"> {{ element.label }} </template
                ><sub>{{ element.octave }}</sub>
              </p>
            </div>
          </draggable>
        </div>
        <p class="theme_label">Mood: {{ gradient_BG.label }}</p>
      </div>

      <!-- BUTTONS -->
      <div class="play flex-center">
        <button :disabled="playing" @click="playSound()">
          Discover your composition
        </button>
        <button v-if="loop" @click="stopSound()">Stop ⬚</button>
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
              <template v-if="displayNotes">
                {{ element.label }}
              </template>
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
import VueContext from "vue-context";

export default {
  components: {
    draggable,
    Color,
    Modale,
    VueContext,
  },
  watch: {
    loop() {
      this.loop ? (this.loopNumber = 10000) : (this.loopNumber = 1);
    },
    sheet: function () {
      this.availableNotes = [
        { name: "C", octave: "4", label: "DO", color: this.gradient[0] },
        { name: "D", octave: "4", label: "RE", color: this.gradient[1] },
        { name: "E", octave: "4", label: "MI", color: this.gradient[2] },
        { name: "F", octave: "4", label: "FA", color: this.gradient[3] },
        { name: "G", octave: "4", label: "SOL", color: this.gradient[4] },
        { name: "A", octave: "4", label: "LA", color: this.gradient[5] },
        { name: "B", octave: "4", label: "SI", color: this.gradient[6] },
        { name: "C#", octave: "4", label: "-", color: "#fff" },
      ];
      this.changeSheetColor();
    },
  },
  data() {
    return {
      loop: false,
      displayNotes: false,
      synth: null,
      sequence: null,
      loopNumber: 1,
      tempo: 1,
      sheet: [
        { name: "C", octave: "4", label: "DO", color: "#c19ee0" },
        { name: "C", octave: "4", label: "DO", color: "#c19ee0" },
        { name: "C", octave: "4", label: "DO", color: "#c19ee0" },
        { name: "D", octave: "4", label: "RE", color: "#b28fd7" },
        { name: "E", octave: "4", label: "MI", color: "#a380cd" },
        { name: "C#", octave: "4", label: "-", color: "#fff" },
        { name: "D", octave: "4", label: "RE", color: "#b28fd7" },
        { name: "C#", octave: "4", label: "-", color: "#fff" },
        { name: "C", octave: "4", label: "DO", color: "#c19ee0" },
        { name: "E", octave: "4", label: "MI", color: "#a380cd" },
        { name: "D", octave: "4", label: "RE", color: "#b28fd7" },
        { name: "D", octave: "4", label: "RE", color: "#b28fd7" },
        { name: "C", octave: "4", label: "DO", color: "#c19ee0" },
      ],
      trash: [],
      availableNotes: [
        { name: "C", octave: "4", label: "DO", color: "#C19EE0" },
        { name: "D", octave: "4", label: "RE", color: "#B185DB" },
        { name: "E", octave: "4", label: "MI", color: "#A06CD5" },
        { name: "F", octave: "4", label: "FA", color: "#9163CB" },
        { name: "G", octave: "4", label: "SOL", color: "#815AC0" },
        { name: "A", octave: "4", label: "LA", color: "#7251B5" },
        { name: "B", octave: "4", label: "SI", color: "#6247AA" },
        { name: "C#", octave: "4", label: "-", color: "#fff" },
      ],
      octaves: ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "10"],
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
      playing: false,
    };
  },
  methods: {
    playSound() {
      this.playing = true;
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
          effect = new Tone.BitCrusher(2).toDestination();
        }
        if (this.gradient_BG.id === 3) {
          this.synth = new Tone.Synth();
          effect = new Tone.Chorus(6, 2.5, 0.5).toDestination().start();
        }
        if (this.gradient_BG.id === 4) {
          this.synth = new Tone.FMSynth();
          effect = new Tone.Chebyshev(2).toDestination();
        }

        this.synth = this.synth.connect(effect);
      } else {
        this.synth = new Tone.Synth();
        this.synth = this.synth.toDestination();
      }
      let i = 0;
      this.sequence = new Tone.Sequence((time, note) => {
        if (note.label === "-")
          this.synth.triggerAttackRelease(
            note.name + note.octave,
            "8t",
            time,
            0
          );
        else
          this.synth.triggerAttackRelease(note.name + note.octave, "8t", time);
        if (this.sheet.length >= i) {
          fireworks(this.gradient, i);
          i++;
          this.playing = i == this.sheet.length ? false : true;
        } else {
          i = 0;
        }
      }, this.sheet);

      this.sequence.loop = this.loopNumber;
      this.sequence.playbackRate = this.tempo;
      this.sequence.start(0);
      Tone.Transport.start();
    },
    stopSound() {
      this.playing = false;
      Tone.Transport.stop();
      Tone.Transport.clear();
      this.sequence.dispose();
    },
    changeSheetColor() {
      this.availableNotes.forEach((exempleNote) => {
        this.sheet.forEach((sheetNote) => {
          if (exempleNote.name === sheetNote.name)
            sheetNote.color = exempleNote.color;
        });
      });
    },
    changeColors(colors, index) {
      this.gradient_BG.id = index;
      this.gradient_BG.label = colors.label;
      this.gradient = colors.colors;
      this.gradient.push("#fff");
      this.availableNotes.forEach((note, i) => (note.color = this.gradient[i]));

      // change les notes déjà entrées
      this.changeSheetColor();

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
    changeOctave(oct, index) {
      this.sheet[index].octave = oct;
    },
  },
  // computed: {
  //   bgButton() {
  //     return (
  //       "color: linear-gradient(0deg, " +
  //       this.gradient[0] +
  //       " 0%, " +
  //       this.gradient[this.gradient.length - 1] +
  //       " 100%)"
  //     );
  //   },
  // },
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

  .ctx-choice {
    color: black;
    padding-left: 16px;
  }
  .ctx-choice:hover {
    color: #888;
    background-color: #ddd;
    cursor: pointer;
  }

  .tempo {
    display: flex;
    flex-direction: row;
    align-items: center;
  }
  .slider {
    -webkit-appearance: none;
    width: 30%;
    height: 10px;
    border-radius: 50px;
    background: #fff;
    outline: none;
    opacity: 0.7;
    -webkit-transition: 0.2s;
    transition: opacity 0.2s;
  }

  .slider::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 16px;
    height: 16px;
    opacity: 1;
    border-radius: 50px;
    background: rgb(179, 179, 179);
    cursor: pointer;
  }
  .slider::-moz-range-thumb {
    width: 16px;
    height: 16px;
    opacity: 1;
    border-radius: 50px;
    background: white;
    cursor: pointer;
  }

  @import "~vue-context/dist/css/vue-context.css";
  @import url("./assets/css/style.css");
</style>
