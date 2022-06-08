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
          <v-btn class="my-6" fab dark large color="white" id="snap" @click="capture()">
          <v-icon v-if="!loading" color="black">mdi-camera</v-icon>
          <v-progress-circular v-if="loading" indeterminate color="black"></v-progress-circular>
          </v-btn>
        </div>

        <div id="error" class="font_text"></div>
        <table style="width:100%" >
          <thead>
          <tr class ="font_text">
            <th>Library</th>
            <th>Raw Output</th>
            <th>Container Number</th>
            <th>ISO Code</th>
          </tr>
          </thead>
          <tbody class="td_font">
          <tr>
            <td>Google</td>
            <td>{{rawOutput_g}}</td>
            <td>{{containerNumber_g}}</td>
            <td>{{isoCode_g}}</td>
          </tr>
          <tr>
          <td>Tesseract</td>
          <td>{{rawOutput_tes}}</td>
          <td>{{containerNumber_tes}}</td>
          <td>{{isoCode_tes}}</td>
          </tr>
          </tbody>
        </table>
        <!-- <div class="font_text">Container Number: {{containerNumber}}</div>
        <div class="font_text">ISO Code: {{isoCode}}</div> -->
  
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
      isoCode_g: '',
      containerNumber_g: '',
      rawOutput_g: '',
      isoCode_tes: '',
      containerNumber_tes: '',
      rawOutput_tes: '',
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
                                                                                                                                                                                              
      try{
        var startTime = performance.now()
        await axios.post("http://127.0.0.1:8000/api/ocr", data)
        var values = await axios.get("http://127.0.0.1:8000/api/ocr");
        console.log(values.data[0]);
        this.rawOutput_g = values.data[0].output;
        this.containerNumber_g = values.data[0].container_number;
        this.isoCode_g = values.data[0].iso;
        this.rawOutput_tes = values.data[1].output;
        this.containerNumber_tes = values.data[1].container_number;
        this.isoCode_tes = values.data[1].iso;
        document.getElementById("error").innerHTML = " ";
        document.getElementById("img").innerHTML = "Image";
      }catch (error){
        document.getElementById("error").innerHTML = "Can't Find Anyting Please Snap Again.";
        document.getElementById("img").innerHTML = "Image";
      }
      this.loading = false;
      var endTime = performance.now()
      console.log(`${endTime - startTime} milliseconds`)
    },
  },
};
</script>

<style>
.font_text {
  font-size: 1.8em;
  font-weight: 100;
  margin-bottom: 1em;
  text-transform: capitalize;
}
.td_font {
  font-size: 1.2em;
  font-weight: 100;
  margin-bottom: 1em;
  text-transform: capitalize;
}
.v-progress-circular {
  margin: 2rem;
}
.webcam{
  display: grid;  
  grid-template-columns: repeat(auto-fit,minmax(240px,1fr));
  margin-block: 50px;
} 

table {
	width: 800px;
  margin: 1.5%;
	border-collapse: collapse;
	overflow: hidden;
	box-shadow: 0 0 20px rgba(0,0,0,0.1);
}

th,
td {
	padding: 15px;
	background-color: rgba(255,255,255,0.2);
	color: #fff;
}

th {
	text-align: left;
}

thead {
		background-color: #67605F;
}

tbody {
		position: relative;
}
</style>
