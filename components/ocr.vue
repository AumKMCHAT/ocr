<template>
  <div class="container">
    <div class="upload">
      <v-file-input
        label="File input"
        filled
        prepend-icon="mdi-camera"
        @change="submit"
      />
<<<<<<< HEAD
      <canvas class="my-4" id="imgCanvas" ref="imgCanvas"></canvas>
    </div>
    <v-col>
    status: 
    {{ status }} <br>

    message:
    {{ message }}
    <div>
      <video ref="video" id="video" width="640" height="480" autoplay></video>
      <button id="snap" @click="capture()">Snap Photo</button>
    </div>
    </v-col>
  </div>
</template>

<script>
import Tesseract from "tesseract.js";

export default {
  data() {
    return {
      dataUrl: "",
      status: "",
      message: "",
      video: {},
      canvas: {},
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
    submit() {
      var self = this;
      var reader,
        files = event.target.files;
      var reader = new FileReader();
      reader.onload = (e) => {
        var img = new Image();
        img.onload = function () {
          self.tesseract(img);
        };
        img.src = event.target.result;
      };

      reader.readAsDataURL(files[0]);
    },

    tesseract(img) {
      var vm = this;
      var canvas = this.$refs.imgCanvas;
      canvas.width = img.width;
      canvas.height = img.height;

      var ctx = canvas.getContext("2d");
      ctx.drawImage(img, 0, 0);
      this.dataUrl = canvas.toDataURL();
      this.status = "reading";
      Tesseract.recognize(this.dataUrl, "eng", {
        logger: (log) => {
          console.log(log);
        },
      })
        .then((result) => {
          this.message = result.data.text;
          vm.status = "";
          this.status = "complete";
        })
        .catch((error) => console.log(error))
        .finally(() => {});
    },

    capture() {
      var self = this;
      this.canvas = this.$refs.imgCanvas;
      var context = this.canvas
        .getContext("2d")
        .drawImage(this.video, 0, 0, 640, 480);

      var canvas = document.getElementById("imgCanvas");
      console.log(canvas)

      var image = new Image();
      image.src = canvas.toDataURL();
      document.getElementById("imgCanvas").appendChild(image);

      image.onload = function () {
        self.tesseract(image);
      };
    },
  },
};
</script>
