<template>
  <div id="app">
    <flow-chart />
  </div>
</template>

<script>
import FlowChart from "./FlowChart.vue";

export default {
  name: "App",

  components: {
    FlowChart
  }
};
</script>

<style lang="scss">
body {
  font-family: Arial, Helvetica, sans-serif;
}
</style>