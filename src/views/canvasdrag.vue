<template>
  <div>
    <div>
      <img id="img" src="" />
      <button id="btn" @click="clickFn">转化为图片且下载</button>
      <!-- <a-button type="danger" @click="pen">画笔</a-button>
      <a-button type="danger" @click="rect">画矩形</a-button>
      <a-button type="danger" @click="circle">画圆</a-button>
      <a-button type="danger" @click="rubber">橡皮</a-button>
      <a-button type="danger" @click="clear">画布清空</a-button> -->
    </div>
    <canvas
      id="canvas"
      width="800"
      height="500"
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
    // 获取Canvas
    window.canvas = document.getElementById("canvas");
    const width = canvas.width;
    const height = canvas.height;
    // 获取绘制上下文
    const ctx = canvas.getContext("2d");
    const images = [
      // {
      //   name: "钉钉",
      //   url: "https://iam-mnt-qa.tongfudun.com/source_image/DingTalk.png",
      // },
      // {
      //   name: "ChainPal",
      //   url: "https://iam-mnt-qa.tongfudun.com/source_image/ChainPal.png",
      // },
      // {
      //   name: "企业微信",
      //   url: "https://iam-mnt-qa.tongfudun.com/source_image/WeCom.png",
      // },
      {
        name: "飞书",
        url: require("./WechatIMG75.png"),
      },
      {
        name: "LDAP",
        url: require("./image.png"),
      },
    ];
    let imagesData = [];
    let clickCoordinate = { x: 0, y: 0 };
    let target;
    ctx.beginPath();
    ctx.strokeStyle = "#333";
    ctx.rect(0, canvas.offsetTop, 20, 20);
    ctx.stroke();
    ctx.beginPath();
    ctx.strokeStyle = "#333";
    ctx.rect(0, canvas.offsetTop + 40, 20, 20);
    ctx.stroke();
    images.forEach((item) => {
      // 创建image元素
      const image = new Image();
      image.setAttribute("crossOrigin", "anonymous");
      image.src = item.url;
      const name = item.name;
      image.onload = () => {
        // 控制宽度为200（等宽）
        const w = 50;
        // 高度按宽度200的比例缩放
        const h = (50 / image.width) * image.height;
        const x = Math.random() * (width - w);
        const y = Math.random() * (height - h);
        const imageObj = { image, name, x, y, w, h };
        imagesData.push(imageObj);
        draw(imageObj);
      };
    });

    // 渲染图片
    function draw(imageObj) {
      ctx.drawImage(
        imageObj.image,
        imageObj.x,
        imageObj.y,
        imageObj.w,
        imageObj.h
      );
      ctx.beginPath();
      ctx.strokeStyle = "#fff";
      ctx.rect(imageObj.x, imageObj.y, imageObj.w, imageObj.h);
      ctx.stroke();
    }

    // 为canvas添加鼠标按下事件
    canvas.addEventListener("mousedown", mousedownFn, false);

    // 鼠标按下触发的方法
    function mousedownFn(e) {
      // 获取元素按下时的坐标
      clickCoordinate.x = e.pageX - canvas.offsetLeft;
      clickCoordinate.y = e.pageY - canvas.offsetTop;
      // 判断选中的元素是哪一个
      checkElement();
      // 未选中元素则return
      if (target == undefined) return;
      // 为canvas添加鼠标移动和鼠标抬起事件
      canvas.addEventListener("mousemove", mousemoveFn, false);
      canvas.addEventListener("mouseup", mouseupFn, false);
    }

    // 鼠标移动触发
    function mousemoveFn(e) {
      const moveX = e.pageX - canvas.offsetLeft;
      const moveY = e.pageY - canvas.offsetTop;
      // 计算移动元素的坐标
      imagesData[target].x = imagesData[target].x + (moveX - clickCoordinate.x);
      imagesData[target].y = imagesData[target].y + (moveY - clickCoordinate.y);
      // 清空画布
      ctx.clearRect(0, 0, width, height);
      // 清空画布以后重新绘制
      imagesData.forEach((i) => draw(i));
      // 赋值
      clickCoordinate.x = moveX;
      clickCoordinate.y = moveY;
    }

    // 鼠标抬起触发
    function mouseupFn() {
      // 鼠标抬起以后移除事件
      canvas.removeEventListener("mousemove", mousemoveFn, false);
      canvas.removeEventListener("mouseup", mouseupFn, false);
      // 销毁选中元素
      target = undefined;
    }

    // 检测选中的元素是哪一个
    function checkElement() {
      ctx.beginPath();
      ctx.strokeStyle = "#333";
      ctx.rect(0, canvas.offsetTop, 20, 20);
      ctx.stroke();
      if (ctx.isPointInPath(clickCoordinate.x, clickCoordinate.y)) {
        console.log("点击的元素a");
      }
      ctx.beginPath();
      ctx.strokeStyle = "#333";
      ctx.rect(0, canvas.offsetTop + 40, 20, 20);
      ctx.stroke();
      if (ctx.isPointInPath(clickCoordinate.x, clickCoordinate.y)) {
        console.log("点击的元素b");
      }

      imagesData.forEach((item, index) => {
        draw(item);
        if (ctx.isPointInPath(clickCoordinate.x, clickCoordinate.y)) {
          target = index;
          console.log("点击的元素是：", item.name);
        }
      });
    }
  },
  methods: {
    // 点击截图函数
    clickFn() {
      var Img = document.getElementById("img");

      // 将canvas转换成base64的url
      let url = window.canvas.toDataURL("image/png");
      // 把Canvas 转化为图片
      Img.src = url;
      // 将base64转换为文件对象
      let arr = url.split(",");
      let mime = arr[0].match(/:(.*?);/)[1]; // 此处得到的为文件类型
      let bstr = atob(arr[1]); // 此处将base64解码
      let n = bstr.length;
      let u8arr = new Uint8Array(n);
      while (n--) {
        u8arr[n] = bstr.charCodeAt(n);
      }
      // 通过以下方式将以上变量生成文件对象，三个参数分别为文件内容、文件名、文件类型
      let file = new File([u8arr], "filename", { type: mime });
      // 将文件对象通过a标签下载
      let aDom = document.createElement("a"); // 创建一个 a 标签
      aDom.download = file.name; // 设置文件名
      let href = URL.createObjectURL(file); // 将file对象转成 UTF-16 字符串
      aDom.href = href; // 放入href
      document.body.appendChild(aDom); // 将a标签插入 body
      aDom.click(); // 触发 a 标签的点击
      document.body.removeChild(aDom); // 移除刚才插入的 a 标签
      URL.revokeObjectURL(href); // 释放刚才生成的 UTF-16 字符串
    },
  },
};
</script>