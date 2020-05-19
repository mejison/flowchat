<template>
  <div class="diagram-area">
    <VueDiagram
      v-if="diagram"
      divClassName="vue-diagram"
      :initDiagram="initDiagram"
      :nodeDataArray="nodeDataArray"
      :linkDataArray="linkDataArray"
      :modelData="modelData"
    />
  </div>
</template>
<script>
import { VueDiagram } from "gojs-vue";
import go from "gojs";

export default {
  name: "DiagramArea",

  components: {
    VueDiagram: VueDiagram
  },

  props: ["nodeDataArray", "linkDataArray", "modelData", "diagram", "instance"],

  methods: {
    showLinkLabel(e) {
      let label = e.subject.findObject("LABEL");
      if (label !== null)
        label.visible = e.subject.fromNode.data.category === "Conditional";
    },
    nodeStyle() {
      return [
        new go.Binding("location", "loc", go.Point.parse).makeTwoWay(
          go.Point.stringify
        ),
        {
          locationSpot: go.Spot.Center
        }
      ];
    },
    makePort(name, align, spot, output, input) {
      let horizontal =
        align.equals(go.Spot.Top) || align.equals(go.Spot.Bottom);
      return this.instance(go.Shape, {
        fill: "transparent",
        strokeWidth: 0,
        width: horizontal ? NaN : 8,
        height: !horizontal ? NaN : 8,
        alignment: align,
        stretch: horizontal
          ? go.GraphObject.Horizontal
          : go.GraphObject.Vertical,
        portId: name,
        fromSpot: spot,
        fromLinkable: output,
        toSpot: spot,
        toLinkable: input,
        cursor: "pointer",
        mouseEnter: function(e, port) {
          if (!e.diagram.isReadOnly) port.fill = "rgba(255,0,255,0.5)";
        },
        mouseLeave: function(e, port) {
          port.fill = "transparent";
        }
      });
    },
    textStyle() {
      return {
        font: "bold 11pt Lato, Helvetica, Arial, sans-serif",
        stroke: "#F8F8F8"
      };
    },
    animateFadeDown(e) {
      this.diagram = e.diagram;
      const animation = new go.Animation();
      animation.isViewportUnconstrained = true;
      animation.easing = go.Animation.EaseOutExpo;
      animation.duration = 900;
      animation.add(
        this.diagram,
        "position",
        this.diagram.position.copy().offset(0, 200),
        this.diagram.position
      );
      animation.add(this.diagram, "opacity", 0, 1);
      animation.start();
    },
    initDiagram() {
      this.diagram.nodeTemplateMap.add(
        "",
        this.instance(
          go.Node,
          "Table",
          this.nodeStyle(),
          this.instance(
            go.Panel,
            "Auto",
            this.instance(
              go.Shape,
              "Rectangle",
              { fill: "#282c34", stroke: "#00A9C9", strokeWidth: 3.5 },
              new go.Binding("figure", "figure")
            ),
            this.instance(
              go.TextBlock,
              this.textStyle(),
              {
                margin: 8,
                maxSize: new go.Size(160, NaN),
                wrap: go.TextBlock.WrapFit,
                editable: true
              },
              new go.Binding("text").makeTwoWay()
            )
          ),

          this.makePort("T", go.Spot.Top, go.Spot.TopSide, false, true),
          this.makePort("L", go.Spot.Left, go.Spot.LeftSide, true, true),
          this.makePort("R", go.Spot.Right, go.Spot.RightSide, true, true),
          this.makePort("B", go.Spot.Bottom, go.Spot.BottomSide, true, false)
        )
      );

      this.diagram.nodeTemplateMap.add(
        "Conditional",
        this.instance(
          go.Node,
          "Table",
          this.nodeStyle(),
          this.instance(
            go.Panel,
            "Auto",
            this.instance(
              go.Shape,
              "Diamond",
              { fill: "#282c34", stroke: "#00A9C9", strokeWidth: 3.5 },
              new go.Binding("figure", "figure")
            ),
            this.instance(
              go.TextBlock,
              this.textStyle(),
              {
                margin: 8,
                maxSize: new go.Size(160, NaN),
                wrap: go.TextBlock.WrapFit,
                editable: true
              },
              new go.Binding("text").makeTwoWay()
            )
          ),
          this.makePort("T", go.Spot.Top, go.Spot.Top, false, true),
          this.makePort("L", go.Spot.Left, go.Spot.Left, true, true),
          this.makePort("R", go.Spot.Right, go.Spot.Right, true, true),
          this.makePort("B", go.Spot.Bottom, go.Spot.Bottom, true, false)
        )
      );

      this.diagram.nodeTemplateMap.add(
        "Start",
        this.instance(
          go.Node,
          "Table",
          this.nodeStyle(),
          this.instance(
            go.Panel,
            "Spot",
            this.instance(go.Shape, "Circle", {
              desiredSize: new go.Size(70, 70),
              fill: "#282c34",
              stroke: "#09d3ac",
              strokeWidth: 3.5
            }),
            this.instance(
              go.TextBlock,
              "Start",
              this.textStyle(),
              new go.Binding("text")
            )
          ),
          this.makePort("L", go.Spot.Left, go.Spot.Left, true, false),
          this.makePort("R", go.Spot.Right, go.Spot.Right, true, false),
          this.makePort("B", go.Spot.Bottom, go.Spot.Bottom, true, false)
        )
      );

      this.diagram.nodeTemplateMap.add(
        "End",
        this.instance(
          go.Node,
          "Table",
          this.nodeStyle(),
          this.instance(
            go.Panel,
            "Spot",
            this.instance(go.Shape, "Circle", {
              desiredSize: new go.Size(60, 60),
              fill: "#282c34",
              stroke: "#DC3C00",
              strokeWidth: 3.5
            }),
            this.instance(
              go.TextBlock,
              "End",
              this.textStyle(),
              new go.Binding("text")
            )
          ),
          // three named ports, one on each side except the bottom, all input only:
          this.makePort("T", go.Spot.Top, go.Spot.Top, false, true),
          this.makePort("L", go.Spot.Left, go.Spot.Left, false, true),
          this.makePort("R", go.Spot.Right, go.Spot.Right, false, true)
        )
      );

      go.Shape.defineFigureGenerator("File", function(shape, w, h) {
        let geo = new go.Geometry();
        let fig = new go.PathFigure(0, 0, true);
        geo.add(fig);
        fig.add(new go.PathSegment(go.PathSegment.Line, 0.75 * w, 0));
        fig.add(new go.PathSegment(go.PathSegment.Line, w, 0.25 * h));
        fig.add(new go.PathSegment(go.PathSegment.Line, w, h));
        fig.add(new go.PathSegment(go.PathSegment.Line, 0, h).close());
        let fig2 = new go.PathFigure(0.75 * w, 0, false);
        geo.add(fig2);
        fig2.add(new go.PathSegment(go.PathSegment.Line, 0.75 * w, 0.25 * h));
        fig2.add(new go.PathSegment(go.PathSegment.Line, w, 0.25 * h));
        geo.spot1 = new go.Spot(0, 0.25);
        geo.spot2 = go.Spot.BottomRight;
        return geo;
      });

      this.diagram.nodeTemplateMap.add(
        "Comment",
        this.instance(
          go.Node,
          "Auto",
          this.nodeStyle(),
          this.instance(go.Shape, "File", {
            fill: "#282c34",
            stroke: "#DEE0A3",
            strokeWidth: 3
          }),
          this.instance(
            go.TextBlock,
            this.textStyle(),
            {
              margin: 8,
              maxSize: new go.Size(200, NaN),
              wrap: go.TextBlock.WrapFit,
              textAlign: "center",
              editable: true
            },
            new go.Binding("text").makeTwoWay()
          )
        )
      );

      this.diagram.linkTemplate = this.instance(
        go.Link,
        {
          routing: go.Link.AvoidsNodes,
          curve: go.Link.JumpOver,
          corner: 5,
          toShortLength: 4,
          relinkableFrom: true,
          relinkableTo: true,
          reshapable: true,
          resegmentable: true,
          mouseEnter: function(e, link) {
            link.findObject("HIGHLIGHT").stroke = "rgba(30,144,255,0.2)";
          },
          mouseLeave: function(e, link) {
            link.findObject("HIGHLIGHT").stroke = "transparent";
          },
          selectionAdorned: false
        },
        new go.Binding("points").makeTwoWay(),
        this.instance(
          go.Shape, // the highlight shape, normally transparent
          {
            isPanelMain: true,
            strokeWidth: 8,
            stroke: "transparent",
            name: "HIGHLIGHT"
          }
        ),
        this.instance(
          go.Shape, // the link path shape
          { isPanelMain: true, stroke: "gray", strokeWidth: 2 },
          new go.Binding("stroke", "isSelected", function(sel) {
            return sel ? "dodgerblue" : "gray";
          }).ofObject()
        ),
        this.instance(
          go.Shape, // the arrowhead
          { toArrow: "standard", strokeWidth: 0, fill: "gray" }
        ),
        this.instance(
          go.Panel,
          "Auto", // the link label, normally not visible
          {
            visible: false,
            name: "LABEL",
            segmentIndex: 2,
            segmentFraction: 0.5
          },
          new go.Binding("visible", "visible").makeTwoWay(),
          this.instance(
            go.Shape,
            "RoundedRectangle", // the label shape
            { fill: "#F8F8F8", strokeWidth: 0 }
          ),
          this.instance(
            go.TextBlock,
            "Yes", // the label
            {
              textAlign: "center",
              font: "10pt helvetica, arial, sans-serif",
              stroke: "#333333",
              editable: true
            },
            new go.Binding("text").makeTwoWay()
          )
        )
      );

      this.diagram.toolManager.linkingTool.temporaryLink.routing =
        go.Link.Orthogonal;
      this.diagram.toolManager.relinkingTool.temporaryLink.routing =
        go.Link.Orthogonal;

      return this.diagram;
    }
  }
};
</script>
<style scss lang="scss">
.diagram-area {
  flex-grow: 1;
  height: 750px;
  background-color: #282c34;
}
</style>