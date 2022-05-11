<template>
  <div>
    <div class="center"><v-btn class="my-6" fab dark large color="blue" id="snap" @click="capture()"><v-icon>mdi-camera</v-icon></v-btn></div><div>
      <p>Camera</p><video ref="video" id="video" width="480" height="320" autoplay></video>
      <canvas ref="canvas" id="canvas" width="480" height="320"></canvas>
    </div>
    
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      video: {},
      canvas: {},
      img: null,
    };
  },
  mounted: function () {
    this.video = this.$refs.video;
    if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
      navigator.mediaDevices
        .getUserMedia({ video: true, audio: false })
        .then((stream) => {
          video.srcObject = stream;
          this.video.play();
        });
    }
  },
  methods: {
    capture() {
      var self = this;
      this.canvas = this.$refs.canvas;
      var context = this.canvas
        .getContext("2d")
        .drawImage(this.video, 0, 0, 480, 320);

      var canvas = document.getElementById("canvas");

      var image = new Image();
      image.src = canvas.toDataURL();
      document.getElementById("canvas").appendChild(image);

      image.onload = function () {
        self.$root.$refs.ocr.drawImage(image);
      };
    },
  },
};
</script>
