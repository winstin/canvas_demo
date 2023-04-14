<template>
  <div>
    <div>
      <a-button type="danger" @click="pen">画笔</a-button>
      <a-button type="danger" @click="rect">画矩形</a-button>
      <a-button type="danger" @click="circle">画圆</a-button>
      <a-button type="danger" @click="rubber">橡皮</a-button>
      <a-button type="danger" @click="clear">画布清空</a-button>
    </div>
    <canvas
      id="myCanvas"
      width="800"
      height="800"
      style="border: 1px solid #000000"
    >
    </canvas>
  </div>
</template>

<script>
export default {
  name: "about",
  data() {
    return {
      editIsSHow: true, //操作
      iseidt: false,
      ctx: null,
      type: "",

      startX: 0,
      startY: 0,
    };
  },
  mounted() {
    var canvas = document.getElementById("myCanvas");
    var ctx = canvas.getContext("2d");
    this.ctx = ctx;
    const self = this;
    const mouseMoving = (e) => {
      if (this.iseidt) {
        switch (self.type) {
          case "pen":
            ctx.lineTo(e.clientX, e.clientY);
            ctx.stroke();
            break;
          case "rect":
            // ctx.restore();
            ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height);

            ctx.putImageData(this.saveImageData, 0, 0);

            ctx.beginPath();

            ctx.lineWidth = 1;
            ctx.strokeStyle = "blue";
            ctx.strokeRect(
              this.startX,
              this.startY,
              e.clientX - this.startX,
              e.clientY - this.startY
            );
            ctx.stroke();
            break;
          case "rubber":
            const r = 30;
            ctx.clearRect(e.clientX - r / 2, e.clientY - r , r, r);
            break;
          case "circle":
            ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height);
            ctx.beginPath();

            ctx.putImageData(this.saveImageData, 0, 0);
            ctx.arc(
              Math.abs(e.clientX + this.startX) / 2,
              Math.abs(e.clientY + this.startY) / 2,
              Math.abs(e.clientX - this.startX),
              0,
              2 * Math.PI
            );
            ctx.stroke();
            ctx.closePath();
            break;
        }
      }
    };

    const mousedown = (e) => {
      this.iseidt = true;
      this.startX = e.clientX;
      this.startY = e.clientY;
      ctx.save();
      switch (self.type) {
        case "pen":
          ctx.lineCap = "round";
          ctx.lineJoin = "round";
          ctx.lineWidth = 5;
          ctx.strokeStyle = "black";
          ctx.beginPath();
          ctx.moveTo(e.clientX, e.clientY);
          break;
        case "rect":
          this.saveImageData = ctx.getImageData(
            0,
            0,
            ctx.canvas.width,
            ctx.canvas.height
          );
          // ctx.save();
          break;
        case "rubber":
          const r = 30;
          ctx.clearRect(e.clientX - r / 2, e.clientY - r, r, r);
          break;
        case "circle":
          this.saveImageData = ctx.getImageData(
            0,
            0,
            ctx.canvas.width,
            ctx.canvas.height
          );

          break;
      }

      canvas.addEventListener("mousemove", mouseMoving, false);
      canvas.addEventListener("mouseup", mouseup, false);

      // console.log(`当前鼠标在Canvas中的位置: x: ${e.clientX}  y: ${e.clientY}`);
    };

    const mouseup = (e) => {
      console.log(`当前鼠标在Canvas中的位置: x: ${e.clientX}  y: ${e.clientY}`);
      // canvas.removeEventListener("mousemove", mouseMoving, false);
      // canvas.removeEventListener("mouseup", mouseup, false);
      ctx.closePath();
      this.iseidt = false;
    };

    canvas.addEventListener("mouseenter", () => {
      canvas.addEventListener("mousedown", mousedown, false);
    });

    canvas.addEventListener("mouseleave", () => {
      this.iseidt = false;
      canvas.removeEventListener("mousedown", mousedown, false);
    });

    // 画线
    function drawLine(position) {
      const { startX, startY, endX, endY } = position;
      ctx.beginPath();
      ctx.moveTo(startX, startY);
      ctx.lineTo(endX || startX, endY || startY);
      ctx.stroke();
    }
  },
  methods: {
    pen() {
      console.log("画笔");
      this.type = "pen";
    },
    rect() {
      console.log("画矩形");
      this.type = "rect";
    },
    rubber() {
      this.type = "rubber";
    },
    circle() {
      this.type = "circle";
    },
    clear() {
      const canvas = document.getElementById("myCanvas");
      const width = canvas.width;
      const height = canvas.height;
      this.ctx.clearRect(0, 0, width, height);
    },
  },
};
</script>