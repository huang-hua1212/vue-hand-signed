<template>
  <div class="handSigned">
    <div>
      <canvas
        id="canvas"
        style="background: #eee; -webkit-transform: translate3d(0, 0, 0); object-fit: cover"
        :width="style.width"
        :height="style.height"
        @touchstart.prevent="handleTouchStart"
        @touchmove.prevent="handleTouchMove"
        @touchend.prevent="handleTouchEnd"
        @mousedown.prevent="handleMouseDown"
        @mousemove.prevent="handleMouseMove"
        @mouseup.prevent="handleMouseUp"
      ></canvas>
      <div>
        <button @click="handleClear">清除</button>
        <button @click="handleConvertToImage">轉圖</button>
      </div>
      <img :src="src" />
    </div>
  </div>
</template>

<script>
import mouseEvent from '../utils/canvas';

export default {
  data() {
    return {
      src: '',
      canvas: null,
      ctx: null,
      style: {
        width: 500,
        height: 400,
      },
      drawing: null,
    };
  },
  mounted() {
    this.style.width = window.innerWidth * 0.7;
    this.canvas = document.getElementById('canvas');
    this.ctx = this.canvas.getContext('2d');
  },
  methods: {
    handleTouchStart(event) {
      this.drawing = true;
      const touchPos = mouseEvent.getTouchPos(this.canvas, event);
      this.ctx.beginPath(touchPos.x, touchPos.y);
      this.ctx.moveTo(touchPos.x, touchPos.y);
      event.preventDefault();
    },
    handleTouchMove(event) {
      if (!this.drawing) return;
      const touchPos = mouseEvent.getTouchPos(this.canvas, event);
      this.ctx.lineWidth = 2;
      this.ctx.lineCap = 'round'; // 繪制圓形的結束線帽
      this.ctx.lineJoin = 'round'; // 兩條線條交匯時，建立圓形邊角
      this.ctx.shadowBlur = 1; // 邊緣模糊，防止直線邊緣出現鋸齒
      this.ctx.shadowColor = 'black'; // 邊緣顏色
      this.ctx.lineTo(touchPos.x, touchPos.y);
      this.ctx.stroke();
    },
    handleMouseDown(event) {
      this.drawing = true;
      const mousePos = mouseEvent.getMousePos(this.canvas, event);
      this.ctx.beginPath();
      this.ctx.moveTo(mousePos.x, mousePos.y);
      event.preventDefault();
    },
    handleTouchEnd() {
      this.drawing = false;
    },
    handleMouseMove(event) {
      if (!this.drawing) return;
      const mousePos = mouseEvent.getMousePos(this.canvas, event);
      this.ctx.lineWidth = 2;
      this.ctx.lineCap = 'round'; // 繪制圓形的結束線帽
      this.ctx.lineJoin = 'round'; // 兩條線條交匯時，建立圓形邊角
      this.ctx.shadowBlur = 1; // 邊緣模糊，防止直線邊緣出現鋸齒
      this.ctx.shadowColor = 'black'; // 邊緣顏色
      this.ctx.lineTo(mousePos.x, mousePos.y);
      this.ctx.stroke();
    },
    handleMouseUp() {
      this.drawing = false;
    },
    handleClear() {
      this.ctx.clearRect(0, 0, this.style.width, this.style.height);
    },
    async handleConvertToImage() {
      const image = this.canvas.toDataURL();
      this.src = image;
      this.$emit('setSignData', this.src);
      this.handleClear();
    },
  },
};
</script>
