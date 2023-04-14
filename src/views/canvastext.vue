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

     // 设置字号为:20px 字体为:Arial
    ctx.font = "20px Arial"; 

    // 默认情况 inherit
    ctx.fillText("携手创作，共同成长", 200, 50);

     // ltr：文本方向从左向右
    ctx.beginPath()
    ctx.direction = "ltr";
    ctx.fillText("携手创作，共同成长", 200, 100);

    // left
    ctx.beginPath()
    ctx.direction = "rtl"; 
    ctx.fillText("携手创作，共同成长", 200, 150);

  },
};
</script>