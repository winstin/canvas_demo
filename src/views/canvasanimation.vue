<template>
  <canvas
    id="myCanvas"
    width="550"
    height="500"
    style="border: 1px solid #000000"
  >
    当前浏览器不支持canvas元素，请升级或更换浏览器！
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
    ctx.globalCompositeOperation = "destination-over";
    const width = c.width;
    const height = c.height;
    let num = 0;
    ctx.strokeStyle = "#ccc";
    const img = new Image();
    img.src = "https://shared-https.ydstatic.com/images/favicon.ico";
    img.onload = () => {
      requestAnimationFrame(planeRun);
    };
    function planeRun() {
      // 清空画布
      ctx.clearRect(0, 0, width, height);

      // 保存画布状态
      ctx.save();

      // 把原心移到画布中间
      ctx.translate(250, 250);

      // 绘制飞机和飞机动画
      num += 0.01;
      ctx.rotate(-num);
      ctx.translate(0, 200);
      ctx.drawImage(img, -20, -20, 40, 40);

      // 恢复状态
      ctx.restore();

      // 飞机运行的轨迹
      ctx.beginPath();
      ctx.arc(250, 250, 200, 0, Math.PI * 2, false);
      ctx.stroke();
      // 执行完以后继续调用
      requestAnimationFrame(planeRun);
    }

   
  },
};
</script>