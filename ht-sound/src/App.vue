<template>
  <div id="app">
    <h1>TONE.JS</h1>

    <!-- HAUTEURS -->
    <label>Hauteur:</label>
    <select v-model="Height">
      <option v-for="height in heights" :value="height" :key="height">
        {{ height }}
      </option>
    </select>

    <!-- EFFETS -->
    <label>Effet:</label>
    <select v-model="Effect">
      <option v-for="effect in effects" :value="effect" :key="effect">
        {{ effect }}
      </option>
    </select>

    <div class="notes-panel">
      <Note
        v-for="note in notes"
        :pitch="note + Height"
        :effect="effect"
        :key="note"
      ></Note>
    </div>

    <div>
      <h2>Drag and Drop</h2>
      <ul
        class="music"
        @drop="onDrop($event)"
        @dragenter.prevent
        @dragover.prevent
      >
        <li v-for="(note, index) in music" :key="index">{{ note }}</li>
      </ul>

      <ul class="color">
        <li
          v-for="(note, index) in notes"
          :key="index"
          draggable="true"
          @dragstart="startDrag($event, note)"
        >
          {{ note }}
        </li>
      </ul>

      <div
        class="trash"
        @drop="trash($event)"
        @dragenter.prevent
        @dragover.prevent
      ></div>
    </div>
  </div>
</template>

<script>
import Note from "./components/Note.vue";

export default {
  name: "App",
  components: {
    Note,
  },
  data() {
    return {
      Height: "4",
      Effect: "none",
      notes: ["C", "D", "E", "F", "G", "A", "B"],
      music: [],
      heights: ["0", "1", "2", "3", "4", "5"],
      effects: ["none", "reverb", "chorus", "bitCrusher"],
    };
  },
  methods: {
    startDrag(event, item) {
      console.log(item);
      event.dataTransfer.dropEffect = "move";
      event.dataTransfer.effectAllowed = "move";
      event.dataTransfer.setData("itemID", item);
    },
    onDrop(event) {
      const itemID = event.dataTransfer.getData("itemID");
      this.music.push(itemID);
    },
    trash(event) {
      const itemID = event.dataTransfer.getData("itemID");
      this.music.push(itemID);
    },
  },
};
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  .notes-panel {
    display: flex;
    flex-direction: row;
    justify-content: center;
  }
  .music {
    background-color: beige;
    min-height: 100px;
  }
  li {
    background-color: rgba(0, 0, 0, 0.3);
    margin: 8px;
  }
  .trash {
    background-color: red;
    min-height: 30px;
  }
</style>
