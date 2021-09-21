<template>
  <main>
    <div class="mt-5 container">
      <div class="header">menu</div>
      <div class="music">
        <div>
          <h4 class="mb-3">Musique</h4>
          <draggable
            class="draggable-list"
            :list="list1"
            :group="{ name: 'myGroup', put: true }"
          >
            <div class="list-item" v-for="element in list1" :key="element.name">
              {{ element.name }}
            </div>
          </draggable>
        </div>
      </div>
      <div class="play flex-center">
        <button>Play</button>
      </div>
      <div class="notes">
        <div class="flex-center">
          <div>
            <draggable
              class="draggable-list"
              :list="list2"
              :group="{ name: 'myGroup', pull: 'clone', put: false }"
            >
              <div
                class="list-item"
                v-for="element in list2"
                :key="element.name"
              >
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
export default {
  components: {
    draggable,
  },
  data() {
    return {
      list1: [],
      list3: [],
      list2: [
        { name: "A" },
        { name: "B" },
        { name: "C" },
        { name: "D" },
        { name: "E" },
        { name: "F" },
        { name: "G" },
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
        console.log("next tick");
        synth.triggerAttackRelease(note.item + this.Height, 0.1, time);
      }, this.music).start(0);
      Tone.Transport.start();

      const analyser = new Tone.Analyser();
      console.log(analyser);
    },
  },
};
</script>
<style scoped>
  @import url("./assets/css/style.css");
</style>