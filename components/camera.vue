<template>
  <div id="el">
    <div>
      <video ref="video" id="video" width="640" height="480" autoplay></video>
    </div>
    <div><button id="snap" @click="capture()">Snap Photo</button></div>
    <canvas ref="canvas" id="canvas" width="640" height="480"></canvas>
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
        .drawImage(this.video, 0, 0, 640, 480);

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
