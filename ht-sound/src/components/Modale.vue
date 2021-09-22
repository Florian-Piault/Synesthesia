<template>
  <div class="modale-container">
      <div class="btn-container">
        <button class="close-modale" @click="closeModale()">
            <svg class="close-svg" width="24" height="24" viewBox="0 0 24 24" fill="#2e3a59" xmlns="http://www.w3.org/2000/svg">
                <path d="M15.59 7L12 10.59L8.41 7L7 8.41L10.59 12L7 15.59L8.41 17L12 13.41L15.59 17L17 15.59L13.41 12L17 8.41L15.59 7Z" fill="#2E3A59"/>
            </svg>
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

    .close-svg:hover {
        filter: opacity(0.4)
    }

    .modale-container {
        height:100vh;
        width: 33vw;
        position: absolute;
        background-color: rgba(255,255,255,0.5);
    }
</style>