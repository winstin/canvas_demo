<template>
  <div
    id="activeImages"
    :style="`background-image:url(${apng});background-size: 100% 100%;height: 100vh;`"
  >
    <!-- <img
      style="width: 100px; height: 100px"
      src="../assets/images/out001.webp"
    />
    <img
      style="width: 100px; height: 100px"
      :src="require('@/assets/logo.png')"
    />
    <img
      style="width: 100px; height: 100px"
      :src="require(`../assets/images/out${activeIndex}.webp`)"
    /> -->
  </div>
</template>


<script>
const getBase64Image = (src) => {
  return new Promise((resolve) => {
    const img = new Image();
    img.crossOrigin = "";
    img.src = src;
    img.onload = function () {
      const canvas = document.createElement("canvas");
      canvas.width = img.width;
      canvas.height = img.height;
      const ctx = canvas.getContext("2d");
      ctx?.drawImage(img, 0, 0, img.width, img.height);
      const ext = img.src.substring(img.src.lastIndexOf(".") + 1).toLowerCase();
      const dataURL = canvas.toDataURL("image/" + ext);
      resolve(dataURL);
    };
  });
};

export default {
  name: "about",
  data() {
    return {
      activeIndex: "001",
      currentIndex: 0,
      data: [],
      imagesMap: {},
      loadIndex: 0,
      apng: require("../assets/images/out001.webp"),
    };
  },

  watch: {
    activeIndex(val) {
      const node = this.imagesMap[val];
      if (node) {
        let activeImages = document.getElementById("activeImages");
        while (activeImages?.firstChild) {
          activeImages.removeChild(activeImages.firstChild);
        }
        activeImages?.appendChild(node);
      } else {
        console.log(val);
      }
    },
  },
  mounted() {
    let imageName = [];
    for (let i = 1; i <= 455; i++) {
      let str = i + "";
      if (str.length == 1) {
        str = "00" + i;
      }
      if (str.length == 2) {
        str = "0" + i;
      }
      imageName.push(str);
    }
    this.data = imageName;

    // this.data.forEach(async (item) => {
    //   let url = require(`../assets/images/out${item}.webp`);
    //   item = await getBase64Image(url);
    // });

    console.log(this.data);

    this.createImagesMap();
  },

  methods: {
    loadImage() {
      if (this.currentIndex == 454) {
        this.activeIndex = "001";
        this.currentIndex = 0;
      } else {
        this.currentIndex++;
        this.activeIndex = this.data[this.currentIndex];
      }
      // requestAnimationFrame(this.loadImage());
    },

    createImagesMap() {
      this.imagesMap = {};
      this.data.forEach((item) => {
        const img = new Image();
        img.style.objectFit = "cover";
        img.style.width = "100%";
        img.style.height = "100vh";
        img.style.display = "block";
        img.src = require(`../assets/images/out${item}.webp`);
        img.onload = () => {
          this.loadIndex++;
          // this.loadImage();
          if (this.loadIndex == this.data.length) {
            setInterval(() => {
              this.loadImage();
            }, 100);
          }
        };
        this.imagesMap[item] = img;
      });
    },
  },
};
</script>