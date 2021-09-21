<template>
  <div id="app">
    <div class="head"></div>
    <div class="notes_container">
      <ul
        class="music"
        @drop="onDrop($event)"
        @dragenter.prevent
        @dragover.prevent
      >
        <li
          v-for="(note, index) in music"
          :key="index"
          draggable="true"
          @dragstart="startDragID($event, note)"
        >
          {{ note.item }}
        </li>
      </ul>
    </div>
    <div class="footer">
      <ul class="notes">
        <li
          v-for="(note, index) in notes"
          :key="index"
          draggable="true"
          @dragstart="startDrag($event, note)"
        >
          <p>{{ note }}</p>
        </li>
        <li
          class="trash"
          @drop="trash($event)"
          @dragover="trashIn = true"
          @dragleave="trashIn = false"
          @dragenter.prevent
          @dragover.prevent
        >
          <img
            :class="trashIn ? 'trashIn' : ''"
            class="trash_img"
            src="./assets/trash.svg"
            alt=""
          />
        </li>
        <li class="play"> 
          <button @click="playSound()">PLAY</button>
        </li>
      </ul>
    </div>
  </div>

</template>
<script>
import * as Tone from 'tone';

export default {
  name: "App",
  data() {
    return {
      Height: "4",
      Effect: "none",
      notes: ["C", "D", "E", "F", "G", "A", "B"],
      music: [],
      heights: ["0", "1", "2", "3", "4", "5"],
      effects: ["distortion", "bitCrusher","chorus", "chebyshev", "none"],
      trashIn: false,
    };
  },
  methods: {
    startDrag(event, item) {
      //console.log(item);
      event.dataTransfer.dropEffect = "move";
      event.dataTransfer.effectAllowed = "move";
      event.dataTransfer.setData("itemID", item);
    },
    startDragID(event, item) {
      //console.log(item.item);
      event.dataTransfer.dropEffect = "move";
      event.dataTransfer.effectAllowed = "move";
      event.dataTransfer.setData("itemIDTrash", item.index);
    },
    onDrop(event) {
      const itemID = event.dataTransfer.getData("itemID");
      //console.log(itemID);
      if (itemID != "") {
        this.music.push({ item: itemID, index: this.music.length });
      }
    },
    trash(event) {
      const itemID = event.dataTransfer.getData("itemIDTrash");
      //console.log(itemID);
      this.music.splice(itemID, 1);
      this.cleanArray();
      //console.log(this.music);
    },
    cleanArray() {
      const music_tmp = [];
      this.music.forEach((element, index) => {
        music_tmp.push({ item: element.item, index: index });
      });
      this.music = music_tmp;
    },
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
        console.log("next tick")
        synth.triggerAttackRelease(note.item + this.Height, 0.1, time);
      }, this.music).start(0);
      Tone.Transport.start();
    
      const analyser = new Tone.Analyser();
      console.log(analyser);
    },
  },
};
</script>

<style>
  @import "./assets/css/reset.css";
  @import "./assets/css/style.css";
  @import "./assets/css/footer.css";
</style>
