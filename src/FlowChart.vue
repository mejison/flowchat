<template>
  <div>
    <div class="flow-chart">
      <div class="nav-bar">
        <components-bar :diagram="diagram" :instance="instance" />
        <parameters-bar :parameters="parameters" @save="onSaveParameters" />
      </div>
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
import { DiagramArea, ComponentsBar, ParametersBar } from "@/components";
import go from "gojs";

export default {
  name: "FlowChart",

  components: {
    ComponentsBar,
    DiagramArea,
    ParametersBar
  },

  data() {
    return {
      imports,
      diagram: false,
      instance: false,
      parameters: [
        {
          value: "",
          key: "param_1"
        },
        {
          value: "",
          key: "param_2"
        },
        {
          value: "",
          key: "param_3"
        }
      ]
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
      console.log("load");
    },
    onSaveParameters(parameters) {
      console.log("onSaveParameters", parameters);
    }
  }
};
</script>

<style lang="scss" scoped>
.flow-chart {
  width: 100%;
  display: flex;
  justify-content: space-between;

  .nav-bar {
    width: 200px;
    margin-right: 2px;
    background-color: #282c34;
    display: flex;
    flex-direction: column;
  }
}
</style>