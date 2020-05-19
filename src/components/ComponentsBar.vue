<template>
  <div class="components-bar" id="palette"></div>
</template>
<script>
import go from "gojs";

export default {
  name: "ComponentsBar",

  props: ["diagram", "instance"],

  data() {
    return {};
  },

  mounted() {
    this.init();
  },

  methods: {
    animateFadeDown(e) {
      var diagram = e.diagram;
      var animation = new go.Animation();
      animation.isViewportUnconstrained = true; // So Diagram positioning rules let the animation start off-screen
      animation.easing = go.Animation.EaseOutExpo;
      animation.duration = 900;
      // Fade "down", in other words, fade in from above
      animation.add(
        diagram,
        "position",
        diagram.position.copy().offset(0, 200),
        diagram.position
      );
      animation.add(diagram, "opacity", 0, 1);
      animation.start();
    },
    init() {
      this.instance(go.Palette, "palette", {
        "animationManager.initialAnimationStyle": go.AnimationManager.None,
        InitialAnimationStarting: this.animateFadeDown,
        nodeTemplateMap: this.diagram.nodeTemplateMap,
        model: new go.GraphLinksModel([
          { category: "Start", text: "Start" },
          { text: "Step" },
          { category: "Conditional", text: "???" },
          { category: "End", text: "End" },
          { category: "Comment", text: "Comment" }
        ])
      });
    }
  }
};
</script>
<style lang="scss">
.components-bar {
  width: 100px;
  margin-right: 2px;
  background-color: #282c34;
}
</style>