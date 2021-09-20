<template>
  <div id="app">
    <h1>TONE.JS</h1>

    <!-- HAUTEURS -->
    <label>Hauteur:</label>
    <select v-model="Height">
      <option v-for="height in heights" :value="height" :key="height"> {{height}}</option>
    </select>

    <!-- EFFETS -->
    <label>Effet:</label>
    <select v-model="Effect">
      <option v-for="effect in effects" :value="effect" :key="effect"> {{effect}}</option>
    </select>

    <div class="notes-panel">
      <Note v-for="note in notes" :pitch="note + Height" :effect="effect" :key="note"></Note>
    </div>
  </div>
</template>

<script>
import Note from './components/Note.vue';

export default {
  name: 'App',
  components: {
    Note,
  },
  data() {
    return {
        Height: '4',
        Effect: 'none',
        notes: [
        'C',
        'D',
        'E',
        'F',
        'G',
        'A',
        'B',
      ],
      heights: [
        '0',
        '1',
        '2',
        '3',
        '4',
        '5',
      ],
      effects: [
        'none',
        'reverb',
        'chorus',
        'bitCrusher'
      ]
    }
  }
}
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
</style>
