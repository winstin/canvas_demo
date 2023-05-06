<template>
  <div>
    <div>
      <a-button type="danger" @click="pen">画笔</a-button>
      <a-button type="danger" @click="rect">画矩形</a-button>
      <a-button type="danger" @click="circle">画圆</a-button>
      <a-button type="danger" @click="fill">填充</a-button>
      <a-button type="danger" @click="rubber">橡皮</a-button>
      <a-button type="danger" @click="clear">画布清空</a-button>
    </div>
    <canvas
      id="myCanvas"
      width="600"
      height="600"
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
            ctx.lineTo(e.offsetX, e.offsetY);
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
              e.offsetX - this.startX,
              e.offsetY - this.startY
            );
            ctx.stroke();
            break;
          case "rubber":
            const r = 30;
            ctx.clearRect(e.offsetX - r / 2, e.offsetY - r, r, r);
            break;
          case "circle":
            ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height);
            ctx.beginPath();

            ctx.putImageData(this.saveImageData, 0, 0);
            ctx.arc(
              Math.abs(e.offsetX + this.startX) / 2,
              Math.abs(e.offsetY + this.startY) / 2,
              Math.abs(e.offsetX - this.startX),
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
      this.startX = e.offsetX;
      this.startY = e.offsetY;
      ctx.save();
      switch (self.type) {
        case "pen":
          ctx.lineCap = "round";
          ctx.lineJoin = "round";
          ctx.lineWidth = 5;
          ctx.strokeStyle = "black";
          ctx.beginPath();
          ctx.moveTo(e.offsetX, e.offsetY);
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
          ctx.clearRect(e.offsetX - r / 2, e.offsetY - r, r, r);
          break;
        case "circle":
          this.saveImageData = ctx.getImageData(
            0,
            0,
            ctx.canvas.width,
            ctx.canvas.height
          );
          break;
        case "fill":
          // return;
          const colorRgba = [0, 255, 0, 255];
          console.log("点击");
          const imgData = ctx.getImageData(0, 0, canvas.width, canvas.height);
          //获取点击坐标的rgba信息
          const clickRgba = getColor(e.offsetX, e.offsetY, imgData.data);

          //通过坐标获取rgba数组
          function getColor(x, y, data) {
            const i = getIndex(x, y);
            return [data[i], data[i + 1], data[i + 2], data[i + 3]];
          }

          //通过坐标x，y获取对应imgData数组的索引
          function getIndex(x, y) {
            return (y * canvas.width + x) * 4;
          }

          //简单地根据绝对值之和判断是否为同颜色区域
          function getDiff(rgba1, rgba2) {
            return (
              Math.abs(rgba2[0] - rgba1[0]) +
              Math.abs(rgba2[1] - rgba1[1]) +
              Math.abs(rgba2[2] - rgba1[2]) +
              Math.abs(rgba2[3] - rgba1[3])
            );
          }

          //判断该坐标是否无需变色
          function stopChange(x, y, imgData) {
            if (x < 0 || y < 0 || x > canvas.width || y > canvas.height) {
              //超出canvas边界
              return true;
            }
            const rgba = getColor(x, y, imgData.data);
            if (getDiff(rgba, clickRgba) >= 100) {
              //色值差距过大
              return true;
            }
            if (getDiff(rgba, colorRgba) === 0) {
              //同颜色，不用改
              return true;
            }
          }

          /**
           * 修改指定ImageData的指定位置像素颜色
           */
          const fillPixel = (colorLayer, pixelPos, color) => {
            colorLayer.data[pixelPos] = color[0];
            colorLayer.data[pixelPos + 1] = color[1];
            colorLayer.data[pixelPos + 2] = color[2];
            colorLayer.data[pixelPos + 3] = color[3];

            return colorLayer;
          };

          let x1 = e.offsetX;
          let y1 = e.offsetY;
          const pixelStack = [[x1, y1, 0]];

          while (pixelStack.length > 0) {
            const newPos = pixelStack.pop();
            const [x, y, type] = newPos;
            let current = getIndex(x, y);
            while (x-- > 0 && matchColor(imgData, current, clickRgba)) {
              current = current - 4;
            }
            ++x;
            current = current + 4;
            let reachTop = false,
              reachBottom = false;
            while (
              x++ < canvas.width &&
              matchColor(imgData, current, clickRgba)
            ) {
              fillPixel(imgData, current, colorRgba);

              if (y > 0) {
                if (
                  !reachTop &&
                  matchColor(imgData, current - 4 * canvas.width, clickRgba)
                ) {
                  pixelStack.push([x, y - 1, 1]);
                  reachTop = true;
                } else if (reachTop) {
                  reachTop = false;
                }
              }

              if (y < canvas.height - 1) {
                if (
                  !reachBottom &&
                  matchColor(imgData, current + 4 * canvas.width, clickRgba)
                ) {
                  pixelStack.push([x, y + 1, 2]);
                  reachBottom = true;
                }else if (reachBottom) {
                  reachTop = false;
                }
              }

              current = current + 4;
            }
          }

          ctx.putImageData(imgData, 0, 0);

          break;
      }

      canvas.addEventListener("mousemove", mouseMoving, false);
      canvas.addEventListener("mouseup", mouseup, false);

      // console.log(`当前鼠标在Canvas中的位置: x: ${e.clientX}  y: ${e.clientY}`);
    };

    const mouseup = (e) => {
      console.log(`当前鼠标在Canvas中的位置: x: ${e.clientX}  y: ${e.clientY}`);
      console.log(`当前鼠标在Canvas中的位置: x: ${e.offsetX}  y: ${e.offsetY}`);

      // canvas.removeEventListener("mousemove", mouseMoving, false);
      // canvas.removeEventListener("mouseup", mouseup, false);
      ctx.closePath();
      this.iseidt = false;
    };

    /**
     * 高效率的填充算法
     * 参考地址: http://www.williammalone.com/articles/html5-canvas-javascript-paint-bucket-tool/
     */
    const efficentFloodFill = (ctx, startX, startY, fillColor) => {
      // 保证 startX 和 startY 是正整数
      // 经测试，在触屏设备中 startX 和 startY 可能是小数，造成填充功能无法正确填充
      startX = Math.round(startX);
      startY = Math.round(startY);
      const pixelStack = [[Math.round(startX), Math.round(startY)]];
      const canvasWidth = ctx.canvas.width,
        canvasHeight = ctx.canvas.height;
      const startPos = (startY * canvasWidth + startX) * 4;
      const colorLayer = ctx.getImageData(0, 0, canvasWidth, canvasHeight);
      const startColor = [
        colorLayer.data[startPos],
        colorLayer.data[startPos + 1],
        colorLayer.data[startPos + 2],
        colorLayer.data[startPos + 3],
      ];

      if (
        startColor[0] === fillColor[0] &&
        startColor[1] === fillColor[1] &&
        startColor[2] === fillColor[2] &&
        startColor[3] === fillColor[3]
      )
        return;

      while (pixelStack.length > 0) {
        const newPos = pixelStack.pop();

        const x = newPos[0];
        let y = newPos[1];

        let pixelPos = (y * canvasWidth + x) * 4;
        console.log("开始", pixelPos, y);
        while (y-- >= 0 && matchColor(colorLayer, pixelPos, startColor)) {
          pixelPos -= canvasWidth * 4;
        }
        console.log("结束", pixelPos, y);
        pixelPos += canvasWidth * 4;
        ++y;

        let reachLeft = false,
          reachRight = false;
        while (
          y++ < canvasHeight - 1 &&
          matchColor(colorLayer, pixelPos, startColor)
        ) {
          fillPixel(colorLayer, pixelPos, fillColor);
          // console.log("填充", pixelPos, y);
          if (x > 0) {
            if (matchColor(colorLayer, pixelPos - 4, startColor)) {
              if (!reachLeft) {
                pixelStack.push([x - 1, y]);
                reachLeft = true;
              } else if (reachLeft) {
                false;
              }
            }
          }

          if (x < canvasWidth - 1) {
            if (matchColor(colorLayer, pixelPos + 4, startColor)) {
              if (!reachRight) {
                pixelStack.push([x + 1, y]);
                reachRight = true;
              } else if (reachRight) {
                false;
              }
            }
          }

          pixelPos += canvasWidth * 4;
        }
      }

      ctx.putImageData(colorLayer, 0, 0);
    };

    /**
     * 判断两个位置的像素颜色是否相同
     */
    const matchColor = (colorLayer, pixelPos, color) => {
      const r = colorLayer.data[pixelPos];
      const g = colorLayer.data[pixelPos + 1];
      const b = colorLayer.data[pixelPos + 2];
      const a = colorLayer.data[pixelPos + 3];

      return (
        r === color[0] && g === color[1] && b === color[2] && a === color[3]
      );
    };

    /**
     * 修改指定ImageData的指定位置像素颜色
     */
    const fillPixel = (colorLayer, pixelPos, color) => {
      colorLayer.data[pixelPos] = color[0];
      colorLayer.data[pixelPos + 1] = color[1];
      colorLayer.data[pixelPos + 2] = color[2];
      colorLayer.data[pixelPos + 3] = color[3];

      return colorLayer;
    };

    const clickCb = (e) => {
      console.log(e.offsetX, e.offsetY);
      // const colorRgba = [0, 255, 0, 255];
      // switch (self.type) {
      //   case "fill":
      //     efficentFloodFill(ctx, e.offsetX, e.offsetY, colorRgba);
      //     break;
      // }
    };

    canvas.addEventListener("mouseenter", () => {
      canvas.addEventListener("mousedown", mousedown, false);
    });

    canvas.addEventListener("mouseleave", () => {
      this.iseidt = false;
      canvas.removeEventListener("mousedown", mousedown, false);
    });

    canvas.addEventListener("click", clickCb);

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
    fill() {
      this.type = "fill";
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