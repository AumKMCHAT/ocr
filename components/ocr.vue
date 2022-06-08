<template>
  <div class="container">
      <v-col class="font-weight-medium">

        <section class="webcam">
         <div>
          <div class="font_text">Camera</div>
          <video ref="video" id="video" width="480" height="360" autoplay></video>
        </div>
        <div>
          <div id="img" class="font_text"></div>
          <canvas id="imgCanvas" width="480" height="360" ref="imgCanvas"></canvas>
        </div> 
        </section>

        <div class="center">
          <v-btn class="my-6" fab dark large color="white" id="snap" @click="capture()"><v-icon color="black">mdi-camera</v-icon></v-btn>
          <v-progress-circular v-show="loading" indeterminate color="white"></v-progress-circular>
        </div>

        <div class="font_text">Raw Output: {{rawOutput}}</div>
        <div class="font_text">Container Number: {{containerNumber}}</div>
        <div class="font_text">ISO Code: {{isoCode}}</div>
  
        <div id="sugIso">
          <p class="main">Suggest Iso: {{ suggestIso }}</p>
        </div>
      </v-col>
  </div>
</template>

<script>
import axios from 'axios';

export default {

  data() {
    return {
      loading: false,
      video: {}, 
      canvas: {},
      isoCode: '',
      containerNumber: '',
      rawOutput: '',
      suggestIso: ''
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

    async capture() {
      this.loading = true;
      this.canvas = this.$refs.imgCanvas;
      var context = this.canvas
        .getContext("2d")
        .drawImage(this.video, 0, 0, 480, 360);
      var canvas = document.getElementById("imgCanvas");
      var dataURL = canvas.toDataURL();
      var dataURLtoBlob = require('dataurl-to-blob'); 
      var blob = dataURLtoBlob(dataURL);
      let data = new FormData();
      data.append('image', blob);
      
      const values = await axios.post("http://127.0.0.1:8000/api/ocr", data)
      const result = await axios.get(`http://127.0.0.1:8000/api/ocr`);

      if (values.data.length > 0){
        this.suggestIso = values.data.join('  ');
      }else{
        this.suggestIso = "Not found Suggest ISO"
      }
      
      this.containerNumber = result.data.container_number;
      this.isoCode = result.data.iso;
      this.rawOutput = result.data.output;
      this.loading = false;
      document.getElementById("img").innerHTML = "Image";
    },
  },
};
</script>

<style>
.font_text {
  font-size: 1.8em;
  font-weight: 100;
  margin-bottom: 1em;
  
}
.v-progress-circular {
  margin: 2rem;
}
.webcam{
  display: grid;  
  grid-template-columns: repeat(auto-fit,minmax(240px,1fr));
  margin-block: 50px;
}
p{
  display: inline-block;
  font-size:  1.4em;
  font-weight: 100;
  margin-bottom: 1em;
  text-transform: capitalize;
}
p.main{
  display: inline-block;
  font-size:  1.8em;
  font-weight: 100;
  margin-bottom: 1em;
  text-transform: capitalize;
}
</style>
