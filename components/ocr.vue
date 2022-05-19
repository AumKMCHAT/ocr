<template>
  <div class="container">
      <v-col class="font-weight-medium">
         <div>
          <video ref="video" id="video" width="480" height="320" autoplay></video>
          <canvas class="mx-4" id="imgCanvas" width="480" height="320" ref="imgCanvas"></canvas>
        </div>
        <div class="center">
          <v-btn class="my-6" fab dark large color="blue" id="snap" @click="capture()"><v-icon>mdi-camera</v-icon></v-btn>
          <v-progress-circular indeterminate color="red"></v-progress-circular>
        </div>
        <div class="font_text">Container_id: {{containerId}}</div>
        <div class="font_text">ISO: {{isoCode}}</div>
        
      </v-col>
  </div>
</template>

<script>
import Tesseract from "tesseract.js";
import axios from 'axios';
import FormData from 'form-data'

export default {

  data() {
    return {
      dataUrl: "",
      status: "",
      message: "",
      video: {},
      canvas: {},
      isoCode: '',
      containerId: ''
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

    dataURItoBlob(dataURI) {
        var byteString = atob(dataURI.split(',')[1]);
        var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0]
        var ab = new ArrayBuffer(byteString.length);
        var ia = new Uint8Array(ab);
        for (var i = 0; i < byteString.length; i++) {
            ia[i] = byteString.charCodeAt(i);
        }
        var blob = new Blob([ab], {type: mimeString});
        return blob;
    },

    async capture() {
     
      var self = this;
      this.canvas = this.$refs.imgCanvas;
      var context = this.canvas
        .getContext("2d")
        .drawImage(this.video, 0, 0, 480, 320);

      var canvas = document.getElementById("imgCanvas");
      var dataURL = canvas.toDataURL();
      var blob = self.dataURItoBlob(dataURL);

      let data = new FormData();
      data.append('image', blob);

      axios
        .post("http://127.0.0.1:8000/api/ocr", 
          data
        )
        .then(function (response) {
          console.log(response);
        })
        .catch(function (error) {
          console.log(error);
        });

      const result = await axios.get(`http://127.0.0.1:8000/api/ocr`);
      this.containerId = result.data[0]+result.data[1];
      this.isoCode = result.data[2];
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
.v-progress-circular {
  margin: 1rem;
}
</style>
