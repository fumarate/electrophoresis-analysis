
<template>
  <div>
    <input type="file" id="upload" @change="onFileChange" />
    <br />
    <input type="button" value="清除画布" @click="canvasClean" /><input
      type="text"
      placeholder="有效数字位数"
      v-model="validNum"
      @change="canvasRefresh"
    />
    <br />
    <canvas
      id="canvas"
      @mousedown="canvasMouseDown"
      @mouseup="canvasMouseUp"
      @mousemove="canvasMouseMove"
    ></canvas>
  </div>
</template>
<script>

export default {
  name: "EAnalyzer",
  components: {
  },
  props: {},
  data() {
    return {
      validNum : 4,
      boxes: [],
      canvas: {},
      ctx: {},
      originalImage: {},
    };
  },
  mounted() {
    this.canvas = document.getElementById("canvas");
    this.ctx = this.canvas.getContext("2d");
  },
  methods: {
    isMobile() {
      return /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(
        navigator.userAgent
      );
    },
    onFileChange(e) {
      const file = e.target.files[0];
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = () => {
        const img = new Image();
        img.src = reader.result;
        this.originalImage = img;
        img.onload = () => {
          this.canvas.width = 400;
          this.canvas.height = (img.height * this.canvas.width) / img.width;
        this.ctx.drawImage(this.originalImage, 0, 0, this.canvas.width, this.canvas.height);
        };
        console.log(this.canvas);
      };
    },

    canvasRefresh() {
      this.ctx.clearRect(0, 0, this.ctx.width, this.ctx.height);
      this.ctx.drawImage(this.originalImage, 0, 0, this.canvas.width, this.canvas.height);
      this.ctx.strokeStyle = "red";
      this.ctx.lineWidth = 2;

      for (var i = -1; i < this.boxes.length; i++) {
        var box= this.boxes[i];
          this.ctx.fillStyle = "red";
          this.ctx.strokeStyle = "red";
        if (box != null) {
          this.ctx.strokeRect(box.x, box.y, box.w, box.h);
          var gr = this.getAreaGreyValue(box); //在绘制rect之前获取灰度值
          gr = gr.toPrecision(this.validNum);
          
          if (box.w < 40 || box.h < 25) {
            this.ctx.fillRect(box.x, box.y - 25, 40, 25);
            this.ctx.fillStyle = "white";
            this.ctx.font = "20px Arial";
            this.ctx.fillText(gr, box.x, box.y - 5);
          } else {
            this.ctx.fillRect(box.x, box.y, 40, 25);
            this.ctx.fillStyle = "white";
            this.ctx.font = "20px Arial";
            this.ctx.fillText(gr, box.x, box.y + 20);
          }
        }
      }
    },
    getAreaGreyValue(box) {
      var imgData = this.ctx.getImageData(box.x, box.y, box.w, box.h);
      var length = imgData.data.length;
      var sum = 0;
      var amount = 0;
      for (var i = 0; i < length; i += 4, amount += 1) {
        var r = imgData.data[i];
        var g = imgData.data[i + 1];
        var b = imgData.data[i + 2];
        var grey = r * 0.299 + g * 0.587 + b * 0.114;
        sum += grey;
      }

      return sum / amount;
    },
    canvasClean() {
      this.boxes = [];
      this.canvasRefresh();
    },
    canvasMouseDown(e) {
      this.startX = e.offsetX;
      this.startY = e.offsetY;
      this.isDrawing = true;
      this.ctx.strokeStyle = "red";
      this.ctx.lineWidth = 2;
    },
    canvasMouseUp(e) {
      this.isDrawing = false;
        this.boxes.push({
          x: this.startX,
          y: this.startY,
          w: e.offsetX - this.startX,
          h: e.offsetY - this.startY,
        });
      this.canvasRefresh();
      console.log(this.validNum)
    },
    canvasMouseMove(e) {
      if (this.isDrawing) {
        this.ctx.strokeRect(
          this.startX,
          this.startY,
          e.offsetX - this.startX,
          e.offsetY - this.startY
        );
      }
    },
  },
};
</script>
<style scoped>
#cropdiv {
  display: none;
}
#canvas{
  border:solid 1px red
}
</style>