<template>
  <div>
    <div class="flow-chart">
      <components-bar :diagram="diagram" :instance="instance" />
      <diagram-area
        :diagram="diagram"
        :instance="instance"
        :nodeDataArray="imports.nodeDataArray"
        :linkDataArray="imports.linkDataArray"
        :modelData="imports.modelData"
      />
    </div>
    <button @click="onHandleLoad">Load</button>
  </div>
</template>

<script>
import imports from "./data.json";
import { DiagramArea, ComponentsBar } from "@/components";
import go from "gojs";

export default {
  name: "FlowChart",

  components: {
    ComponentsBar,
    DiagramArea
  },

  data() {
    return {
      imports,
      diagram: false,
      instance: false
    };
  },

  mounted() {
    this.init();
  },

  methods: {
    init() {
      this.instance = go.GraphObject.make;
      const diagram = this.instance(go.Diagram, {
        "undoManager.isEnabled": true,
        model: this.instance(go.GraphLinksModel, {
          linkKeyProperty: "key"
        })
      });

      this.diagram = diagram;
    },
    onHandleLoad() {
      console.log("export");
    }
  }
};
</script>

<style lang="scss" scoped>
.flow-chart {
  width: 100%;
  display: flex;
  justify-content: space-between;
}
</style>