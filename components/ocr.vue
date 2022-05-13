<template>
  <div class="container">
      <v-col class="font-weight-medium">
         <div>
          <video ref="video" id="video" width="480" height="320" autoplay></video>
          <canvas class="mx-4" id="imgCanvas" width="480" height="320" ref="imgCanvas"></canvas>
        </div>
        <div class="center">
          <v-btn class="my-6" fab dark large color="blue" id="snap" @click="capture()"><v-icon>mdi-camera</v-icon></v-btn>
        </div>
        <p class = "font_text">
        status: 
        {{ status }} <br>

        message:
        {{ message }}
        </p>
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
      this.progressBar = true;
      var self = this;
      this.canvas = this.$refs.imgCanvas;
      var context = this.canvas
        .getContext("2d")
        .drawImage(this.video, 0, 0, 480, 320);

      var canvas = document.getElementById("imgCanvas");
      console.log(canvas)

      var image = new Image();
      image.src = canvas.toDataURL();
      document.getElementById("imgCanvas").appendChild(image);

      image.onload = function () {
        self.tesseract(image);
      };
      this.progressBar = false;
    },
  },
};
</script>

<style>
.font_text {
  font-size: 1.4em;
  font-weight: 100;
  text-transform: capitalize;
}
</style>
