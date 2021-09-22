<template>
  <main>
    <div class="mt-5 container">
      <!--  <canvas class="fireworks"></canvas> -->
      <div class="header">menu</div>
      <div class="music">
        <div>
          <draggable
            class="draggable-list"
            :list="sheet"
            :group="{ name: 'myGroup', put: true }"
          >
            <div class="list-item" v-for="element in sheet" :key="element.name">
              {{ element.label }}
            </div>
          </draggable>
        </div>
      </div>
      <div class="play flex-center column">
        <button @click="playSound()" class="btn-hover color-1">
          See your song
        </button>

        <!--  <label>Découvrez visuellement votre musique</label> -->
      </div>
      <div class="notes">
        <div class="flex-center">
          <div>
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
                {{ element.label }}
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
import * as Tone from "tone";
/* import { fireworks } from "./assets/js/fireworks"; */

export default {
  components: {
    draggable,
  },
  data() {
    return {
      sheet: [],
      trash: [],
      availableNotes: [
        { name: "C", label: "DO" },
        { name: "D", label: "RE" },
        { name: "E", label: "MI" },
        { name: "F", label: "FA" },
        { name: "G", label: "SOL" },
        { name: "A", label: "LA" },
        { name: "B", label: "SI" },
      ],
    };
  },
  methods: {
    playSound() {
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
      }, this.sheet).start(0);
      Tone.Transport.start();

      // const analyser = new Tone.Analyser();
      // console.log(analyser);
    },
  },
};
</script>
<style scoped>
  @import url("./assets/css/style.css");
</style>
