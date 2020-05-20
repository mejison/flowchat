<template>
  <input class="parameter" :placeholder="placeholder" :value="value" @input="onChange" />
</template>
<script>
export default {
  name: "Input",

  props: ["value", "placeholder"],

  data() {
    return {
      payload: this.value
    };
  },

  methods: {
    onChange() {
      this.$emit("input", this.payload);
    }
  }
};
</script>
<style lang="scss">
.parameter {
  width: 100%;
}
</style>