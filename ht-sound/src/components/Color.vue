<template>
  <div>
    <div class="color-container">
      <option class="color-picker" @click="getColors()" :style="bgColor">{{
        theme.label
      }}</option>
    </div>
  </div>
</template>

<script>
import * as chroma from "chroma-js";

export default {
  props: ["theme"],
  data() {
    return {
      bgColor: "",
    };
  },
  methods: {
    getColors() {
      const colors = chroma
        .scale([this.theme.colorFrom, this.theme.colorTo])
        .mode("lch")
        .colors(7);
      this.$emit("changeColors", { colors: colors, label: this.theme.label });
    },
  },
  mounted() {
    const colors = chroma
      .scale([this.theme.colorFrom, this.theme.colorTo])
      .mode("lch")
      .colors(7);
    this.bgColor =
      "background: linear-gradient(90deg, " +
      colors[0] +
      " 0%, " +
      colors[colors.length - 1] +
      " 100%";
  },
};
</script>

<style scoped>
.color-container {
  margin: 8px 0;
}

.color-picker {
  cursor: pointer;
  border-radius: 4px;
  height: 48px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.color-picker:hover {
  filter: contrast(1.4);
}
</style>
