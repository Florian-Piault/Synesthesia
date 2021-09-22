<template>
  <div class="modale-container">
      <div class="btn-container">
        <button class="close-modale" @click="closeModale()">
            <img src="@/assets/close.svg" alt="close" class="close-modale">
        </button>
      </div>
      <slot></slot>
  </div>
</template>

<script>
export default {
    name: 'Modale',
    methods: {
        closeModale() {
            this.$emit('closeModale');
        }
    }

}
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
        filter: opacity(0.7)
    }

    .modale-container {
        z-index: 10;
        height:100vh;
        width: 512px;
        position: absolute;
        background-color: rgba(200,200,200,0.9);
        padding: 16px 32px;
    }
</style>