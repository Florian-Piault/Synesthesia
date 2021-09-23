<template>
  <div class="modale-container">
    <div class="btn-container">
      <button class="close-modale button-modale" @click="closeModale()">
        <img src="@/assets/close.svg" alt="close" class="close-modale" />
      </button>
    </div>
    <slot></slot>
  </div>
</template>
<script>
export default {
  name: "Modale",
  props: ["tempo"],
  data() {
    return { actualTempo: this.tempo };
  },
  methods: {
    closeModale() {
      this.$emit("closeModale");
    },
  },
};
</script>

<style scoped>
.btn-container {
  width: 100%;
  height: 64px;
  display: flex;
  justify-content: flex-end;
  align-items: center;
}

.close-modale {
  display: flex;
  border: none;
  background: none;
  cursor: pointer;
}

.close-modale:hover {
  filter: opacity(0.7);
}

.modale-container {
  z-index: 10;
  width: 512px;
  height: 100vh;
  position: absolute;
  background-color: rgba(255, 255, 255, 0.3);
  backdrop-filter: blur(7px);
  padding: 0px 32px 0;
  border-top-right-radius: 10px;
  border-bottom-right-radius: 10px;
}
</style>
