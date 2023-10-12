<template>
  <div class="page">
    <canvas
      ref="canvas"
      height="600"
      width="600"
      @mousedown="startDrawing"
      @mousemove="draw"
      @mouseup="stopDrawing"
      @mouseleave="stopDrawing"
      @touchstart="startDrawingTouch"
      @touchmove="drawTouch"
      @touchend="stopDrawing"
    ></canvas>
    <div>
      <button @click="clearCanvas">Clear</button>
      <button @click="saveDrawing">Save</button>
      <button @click="redrawDrawing">Redraw</button>
    </div>
    <div>
      <button @click="newDrawing">New Drawing</button>
      <label
        >Save Location:
        <select v-model="selectedSaveLocation">
          <option
            v-for="(location, index) in saveLocations"
            :value="index"
            :key="index"
          >
            {{ location.name }}
          </option>
        </select>
      </label>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      drawing: false,
      lastX: 0,
      lastY: 0,
      ctx: null,
      savedDrawing: null,
      saveLocations: [], // Array to store saved drawings
      selectedSaveLocation: null, // Currently selected save location
    };
  },
  mounted() {
    this.ctx = this.$refs.canvas.getContext("2d");
  },
  methods: {
    startDrawing(event) {
      this.drawing = true;
      this.lastX = event.offsetX;
      this.lastY = event.offsetY;
    },
    startDrawingTouch(event) {
      // Handle touch start event
      event.preventDefault();
      const touch = event.touches[0];
      this.drawing = true;
      this.lastX = touch.clientX - this.$refs.canvas.getBoundingClientRect().left;
      this.lastY = touch.clientY - this.$refs.canvas.getBoundingClientRect().top;
    },
    drawTouch(event) {
      // Handle touch move event
      event.preventDefault();
      if (!this.drawing) return;
      const touch = event.touches[0];
      this.ctx.strokeStyle = "black";
      this.ctx.lineWidth = 40;
      this.ctx.lineJoin = "round";
      this.ctx.lineCap = "round";
      
      this.ctx.beginPath();
      this.ctx.moveTo(this.lastX, this.lastY);
      this.ctx.lineTo(
        touch.clientX - this.$refs.canvas.getBoundingClientRect().left,
        touch.clientY - this.$refs.canvas.getBoundingClientRect().top
      );
      this.ctx.stroke();
      
      this.lastX = touch.clientX - this.$refs.canvas.getBoundingClientRect().left;
      this.lastY = touch.clientY - this.$refs.canvas.getBoundingClientRect().top;
    },
    draw(event) {
      if (!this.drawing) return;
      this.ctx.strokeStyle = "black";
      this.ctx.lineWidth = 40;
      this.ctx.lineJoin = "round";
      this.ctx.lineCap = "round";

      this.ctx.beginPath();
      this.ctx.moveTo(this.lastX, this.lastY);
      this.ctx.lineTo(event.offsetX, event.offsetY);
      this.ctx.stroke();

      this.lastX = event.offsetX;
      this.lastY = event.offsetY;
    },
    stopDrawing() {
      this.drawing = false;
    },
    clearCanvas() {
      this.ctx.clearRect(
        0,
        0,
        this.$refs.canvas.width,
        this.$refs.canvas.height
      );
    },
    saveDrawing() {
      const image = this.$refs.canvas.toDataURL();
      if (this.selectedSaveLocation !== null) {
        this.saveLocations[this.selectedSaveLocation].drawing = image;
      } else {
        this.saveLocations.push({ name: "New Location", drawing: image });
        this.selectedSaveLocation = this.saveLocations.length - 1;
      }
    },
    redrawDrawing() {
      if (this.selectedSaveLocation !== null) {
        const savedImage =
          this.saveLocations[this.selectedSaveLocation].drawing;
        if (savedImage) {
          const img = new Image();
          img.src = savedImage;
          img.onload = () => {
            this.ctx.clearRect(
              0,
              0,
              this.$refs.canvas.width,
              this.$refs.canvas.height
            );
            this.ctx.drawImage(img, 0, 0);
          };
        }
      }
    },
    newDrawing() {
      this.selectedSaveLocation = null;
      this.clearCanvas();
    },
  },
};
</script>

<style scoped>
.page {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
canvas {
  border: 1px solid #bdbdbd;
  background: rgb(109, 100, 100);
}
</style>
