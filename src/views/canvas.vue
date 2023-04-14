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

    // // 画矩形
    // ctx.fillStyle = "#FF0000";
    // // ctx.fillRect(50, 25, 150, 75);

    // // 画线
    // ctx.strokeStyle = "blue";
    // ctx.moveTo(10, 20); // 开始坐标
    // ctx.lineTo(200, 100); // 结束坐标
    // ctx.stroke(); // 绘制线条我们必须使用到 "ink" 的方法，就像stroke().
    // ctx.strokeStyle = "black";
    // // 画圆
    // ctx.beginPath();
    // ctx.lineWidth = 10
    // ctx.arc(50, 50, 20, 0, 2 * Math.PI);
    // ctx.stroke();

    // // 使用 "Arial" 字体在画布上绘制一个高 30px 的文字（实心）：
    // ctx.font = "20px Arial";
    // ctx.fillText("Hello World", 0, 20);

    // // 创建渐变
    // var grd = ctx.createLinearGradient(0, 0, 200, 0);
    // grd.addColorStop(0, "red");
    // // grd.addColorStop(0.5, "yellow");
    // grd.addColorStop(1, "yellow");

    // // 填充渐变
    // ctx.fillStyle = grd;
    // ctx.fillRect(10, 10, 150, 80);

    // // 创建渐变
    // var grd = ctx.createRadialGradient(75, 50, 5, 90, 60, 100);
    // grd.addColorStop(0, "red");
    // grd.addColorStop(0.8, "yellow");
    // grd.addColorStop(0.4, "pink");
    // grd.addColorStop(1, "white");

    // // 填充渐变
    // ctx.fillStyle = grd;
    // ctx.fillRect(10, 10, 150, 80);

    // ctx.rect(50, 50, 200, 100); // 绘制矩形
    // ctx.stroke(); // 描边一个矩形轮廓
    // ctx.beginPath(); // 开启新路径
    // ctx.rect(50, 200, 200, 100); // 绘制矩形
    // ctx.fill(); // 填充一个矩形

    // ctx.beginPath(); // 开启新路径
    // ctx.arc(50, 50, 20, 0, 2 * Math.PI);
    // ctx.stroke(); // 描边一个矩形轮廓;


    // lineCap 值为 butt
    ctx.lineWidth = 20;
    ctx.lineCap='butt'
    ctx.moveTo(50, 50);
    ctx.lineTo(400, 50);
    ctx.stroke();

    // lineCap 值为 round
    ctx.beginPath()
    ctx.lineWidth = 20;
    ctx.lineCap='round'
    ctx.moveTo(50, 100);
    ctx.lineTo(400, 100);
    ctx.stroke();

    // lineCap 值为 square
    ctx.beginPath()
    ctx.lineWidth = 20;
    ctx.lineCap='square'
    ctx.moveTo(50, 150);
    ctx.lineTo(400, 150);
    ctx.stroke();


     ctx.strokeStyle = "#f00" // 描边样式设置为红色
    ctx.lineWidth = 5

    // 绘制一条虚线
    ctx.setLineDash([10, 10, 10]);
    console.log(ctx.getLineDash()); // [5, 10, 20, 5, 10, 20]
    ctx.beginPath();
    ctx.moveTo(0,100);
    ctx.lineTo(500, 100);
    ctx.stroke();

    // 再绘制一条虚线
    ctx.setLineDash([10, 10, 20, 40]);
    console.log(ctx.getLineDash()); // [5, 10, 20, 40]
    ctx.beginPath();
    ctx.moveTo(0,200);
    ctx.lineTo(500, 200);
    ctx.stroke();

  },
};
</script>