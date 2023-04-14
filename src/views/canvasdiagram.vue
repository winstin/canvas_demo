<template>
  <canvas
    id="myCanvas"
    width="500"
    height="500"
    style="border: 1px solid #000000"
  >
  </canvas>
</template>

<script>
export default {
  name: "about",
  data() {
    return {
      editIsSHow: true, //操作
    };
  },
  mounted() {
    var c = document.getElementById("myCanvas");
    var ctx = c.getContext("2d");
    ctx.strokeStyle = "#f00"; // 描边样式设置为红色
    ctx.fillStyle = "#00f"; // 填充样式设置为蓝色
    (ctx.lineWidth = 5),
      // 画圆
      ctx.arc(100, 100, 50, 0, (360 * Math.PI) / 180);
    ctx.stroke();

    // ellipse(x, y, radiusX, radiusY, rotation, startAngle, endAngle, anticlockwise)
    // x、y：椭圆的圆心坐标
    // radiusX：x轴的半径大小
    // radiusY：y轴的半径大小
    // rotation：椭圆的旋转角度，也是以弧度表示
    // startAngle：开始绘制点
    // endAngle：结束绘制点
    // anticlockwise：可选参数，表示绘制的方向（默认顺时针）。
    ctx.beginPath();
    ctx.ellipse(100, 150, 50, 100, (90 * Math.PI) / 180, 0, 2 * Math.PI);
    ctx.stroke();
    ctx.beginPath();
    ctx.ellipse(400, 150, 50, 100, 0, 0, 2 * Math.PI);
    ctx.stroke();
    ctx.beginPath();
    ctx.ellipse(250, 350, 50, 100, Math.PI / 2, 0, 2 * Math.PI); // 旋转90°
    ctx.fill();

    ctx.strokeStyle = "#333";
    ctx.beginPath();
    ctx.moveTo(100, 250);
    ctx.quadraticCurveTo(250, 100, 400, 250);
    ctx.stroke();

    ctx.strokeStyle = "#f00"; // 描边样式设置为红色
    ctx.fillStyle = "#00f"; // 填充样式设置为蓝色
    (ctx.lineWidth = 5),
      // 绘制一个矩形边框
      ctx.beginPath();
    ctx.strokeStyle = "rgba(255, 0, 0, 0.7)"; // 指定透明度的描边样式
    ctx.strokeRect(10, 10, 300, 100);

    // 绘制一个矩形
    ctx.beginPath();
    ctx.fillStyle = "rgba(0, 255, 0, 0.2)"; // 指定透明度的填充样式
    ctx.fillRect(10, 90, 100, 300);

    // 绘制一个圆
    ctx.beginPath();
    ctx.fillStyle = "rgba(255, 255, 0, 1)";
    ctx.globalAlpha = 0.2; // 设置透明度值
    ctx.arc(200, 200, 100, 0, Math.PI * 2, true);
    ctx.fill();

    ctx.beginPath();
    ctx.globalAlpha = 0.5; // 设置透明度值
    ctx.lineWidth = 1;
    ctx.font = "20px Arial";
    ctx.fillStyle = "red";
    ctx.fillText("2222222", 200, 200);


    // 创建一个 image对象
      var img = new Image();
      img.src = "https://www.baidu.com/img/PCfb_5bf082d29588c07f842ccde3f97243ea.png";
      img.onload = function() {
        // 图片加载完以后
        // 创建图案
        var ptrn = ctx.createPattern(img, 'no-repeat');
        ctx.fillStyle = ptrn;
        ctx.fillRect(0, 0, 300, 300);
      }
  },
};
</script>