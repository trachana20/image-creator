<template>
    
    <div>
      <v-stage
        style="width:400px; height:400px; border: 2px solid #000000;"//sets black color border of 2px.
        ref="stage"
        :config="stageSize"
        @mousedown="handleStageMouseDown"
        @touchstart="handleStageMouseDown"
      >
        <v-layer ref="layer">
          <v-image
            v-for="item in images"
            :key="item.id"
            :config="item"
          />
          <v-transformer ref="transformer" />
        </v-layer>
      </v-stage>
    </div>
</template>

<script>

export default {
    name: 'Editor',
    props: ['images', 'index'],
    data() {
    return {
      stageSize: {
        width: 400,
        height: 400,
      },
      image: new Image(100, 100),
      editIndex: this.index,
      selectedShapeName: "",
      editImages: [],
    
      }
  },
  methods: {

    handleTransformEnd(e) {
      const name = String((this.images[this.editIndex].url).split('o/')[1].split('.')[0]);
      const rect = this.images.find((r) => r.name === name);
    
      rect.x = e.target.x();
      rect.y = e.target.y();
      rect.rotation = e.target.rotation();
      rect.scaleX = e.target.scaleX();
      rect.scaleY = e.target.scaleY();
    },
    handleStageMouseDown(e) {
      if (e.target === e.target.getStage()) {
        console.log("e.target",e.target);
        this.selectedShapeName = "";
        this.updateTransformer();
        return;
      }

      const clickedOnTransformer =
        e.target.getParent().className === "Transformer";
      if (clickedOnTransformer) {
        return;
      }

      const name = String((this.images[this.editIndex].url).split('o/')[1].split('.')[0]);
      const rect = this.images.find((r) => r.name === name);
    
      if (rect) {
        this.selectedShapeName = name;
      } else {
        this.selectedShapeName = "";
      }
      this.updateTransformer();
    },
    updateTransformer() {
      const transformerNode = this.$refs.transformer.getNode();
      console.log("transformer", transformerNode);
      const stage = transformerNode.getStage();
      const { selectedShapeName } = this;

      console.log("stage0", stage.findOne(".code.gif"));


      console.log("selectedShape", selectedShapeName);
    
      const selectedNode = stage.findOne("." + selectedShapeName);

      console.log("selectedNode", selectedNode);

      if (selectedNode === transformerNode.node()) {
        return;
      }

      if (selectedNode) {
        transformerNode.nodes([selectedNode]);
      } else {
        transformerNode.nodes([]);
      }
      transformerNode.getLayer().batchDraw();
    },
  },
  computed: {
    configImg() {

      return {
        x: 50,
        y: 50,
        image: this.image,
        editIndex: this.index,
        // image.src: this.images[this.editIndex],
        width: 200,
        height: 200,
        draggable: true,
      }
    },
  },
  watch: {
      index() {
       this.editIndex = this.index;
       this.image.src = this.images[this.editIndex].url;
      }
  },

}
</script>

<style lang="scss" scoped>

</style>
