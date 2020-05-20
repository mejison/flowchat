<template>
  <div class="parameters-bar">
    <h3 class="parameters-bar-title">Parameters</h3>
    <div class="parameters">
      <div class="parameters-item" v-for="(option, index) in options" :key="index">
        <div class="parameters-key">
          <label>Key:</label>
          <parameter :placeholder="options[index].placeholder" v-model="options[index].key" />
        </div>
        <div class="parameters-value">
          <label>Value:</label>
          <parameter :placeholder="options[index].placeholder" v-model="options[index].value" />
        </div>
      </div>
    </div>
    <button @click.prevent="onClickSave" class="btn-save">Save</button>
  </div>
</template>
<script>
import Parameter from "./Parameter";

export default {
  name: "parameters-bar",

  components: {
    parameter: Parameter
  },

  props: ["parameters"],

  data() {
    return {
      options: this.parameters
    };
  },

  methods: {
    onClickSave() {
      this.$emit("save", this.options);
    }
  }
};
</script>
<style lang="scss">
.parameters-bar {
  padding: 15px 10px;
  margin-top: auto;

  .parameters-bar-title {
    color: #fff;
  }

  .parameters {
    padding: 15px 0;

    .parameters-item {
      display: flex;
      flex-direction: column;
      margin-bottom: 15px;
      border: 1px solid #fff;
      padding: 15px;
      color: #fff;
    }
  }

  .btn-save {
  }
}
</style>