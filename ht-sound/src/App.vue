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
      <div class="notes flex-center">
        <div>
          <h4 class="mb-3">Notes</h4>
          <draggable
            class="draggable-list"
            :list="list2"
            :group="{ name: 'myGroup', pull: 'clone', put: false }"
          >
            <div class="list-item" v-for="element in list2" :key="element.name">
              {{ element.name }}
            </div>
          </draggable>
        </div>

        <div>
          <h4 class="mb-3">Poubelle</h4>
          <draggable
            class="draggable-list trash"
            :list="list3"
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
};
</script>
<style scoped>
  @import url("./assets/css/style.css");
</style>