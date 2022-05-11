<template>
  <div class="container">
    <div class="upload">
      <v-file-input
        label="File input"
        filled
        prepend-icon="mdi-camera"
        @change="submit"
      />
      <canvas id="imgCanvas" ref="imgCanvas"></canvas>
    </div>
    status: 
    {{ status }} <br>

    message:
    {{ message }}
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
    };
  },

  created() {
    this.$root.$refs.ocr = this;
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
          self.drawImage(img);
        };
        img.src = event.target.result;
      };

      reader.readAsDataURL(files[0]);
    },

    drawImage(img) {
      var vm = this;
      var canvas = this.$refs.imgCanvas;
      canvas.width = img.width;
      canvas.height = img.height;

      var ctx = canvas.getContext("2d");
      ctx.drawImage(img, 0, 0);
      this.dataUrl = canvas.toDataURL();
      this.status = "reading";
      Tesseract.recognize(this.dataUrl, "eng", {
        logger: log => {
          console.log(log);
        },
      })
        .then(result => {
          
          this.message = result.data.text;
          vm.status = "";
          this.status = "complete";
        })
        .catch(error => console.log(error))
        .finally(() => {});
    },
  },
};
</script>
