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
      </ul>
    </div>
  </div>
</template>
<script>
export default {
  name: "App",
  data() {
    return {
      Height: "4",
      Effect: "none",
      notes: ["C", "D", "E", "F", "G", "A", "B"],
      music: [],
      heights: ["0", "1", "2", "3", "4", "5"],
      effects: ["none", "reverb", "chorus", "bitCrusher"],
      trashIn: false,
    };
  },
  methods: {
    startDrag(event, item) {
      console.log(item);
      event.dataTransfer.dropEffect = "move";
      event.dataTransfer.effectAllowed = "move";
      event.dataTransfer.setData("itemID", item);
    },
    startDragID(event, item) {
      console.log(item.item);
      event.dataTransfer.dropEffect = "move";
      event.dataTransfer.effectAllowed = "move";
      event.dataTransfer.setData("itemIDTrash", item.index);
    },
    onDrop(event) {
      const itemID = event.dataTransfer.getData("itemID");
      console.log(itemID);
      if (itemID != "") {
        this.music.push({ item: itemID, index: this.music.length });
      }
    },
    trash(event) {
      const itemID = event.dataTransfer.getData("itemIDTrash");
      console.log(itemID);
      this.music.splice(itemID, 1);
      this.cleanArray();
      console.log(this.music);
    },
    cleanArray() {
      const music_tmp = [];
      this.music.forEach((element, index) => {
        music_tmp.push({ item: element.item, index: index });
      });
      this.music = music_tmp;
    },
  },
};
</script>

<style>
  @import "./assets/css/reset.css";
  @import "./assets/css/style.css";
  @import "./assets/css/footer.css";
</style>
