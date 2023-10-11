<template>
    <div>
      <canvas
        ref="canvas"
        :width="canvasWidth"
        :height="canvasHeight"
        @mousedown="startDrawing"
        @mousemove="draw"
        @mouseup="stopDrawing"
        @touchstart="startDrawing"
        @touchmove="draw"
        @touchend="stopDrawing"
      ></canvas>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        isDrawing: false,
        canvasWidth: 400,
        canvasHeight: 400,
      };
    },
    mounted() {
      this.canvas = this.$refs.canvas;
      this.ctx = this.canvas.getContext('2d');
      this.ctx.lineWidth = 2;
      this.ctx.lineJoin = 'round';
      this.ctx.lineCap = 'round';
    },
    methods: {
      startDrawing(event) {
        this.isDrawing = true;
        const [x, y] = this.getMousePosition(event);
        this.ctx.beginPath();
        this.ctx.moveTo(x, y);
      },
      draw(event) {
        if (!this.isDrawing) return;
        const [x, y] = this.getMousePosition(event);
        this.ctx.lineTo(x, y);
        this.ctx.stroke();
      },
      stopDrawing() {
        this.isDrawing = false;
      },
      getMousePosition(event) {
        const rect = this.canvas.getBoundingClientRect();
        const touch = event.touches ? event.touches[0] : event;
        return [touch.clientX - rect.left, touch.clientY - rect.top];
      },
    },
  };
  </script>
  