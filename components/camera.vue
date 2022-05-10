<template>
  <div id="el">
        <div><video ref="video" id="video" width="640" height="480" autoplay></video></div>
        <div><button id="snap" v-on:click="capture()">Snap Photo</button></div>
        <canvas ref="canvas" id="canvas" width="640" height="480"></canvas>
</div>
</template>

<script>
export default {
	data: function () {
		return {
			video: {},
			canvas: {},
			captures: []
		};
	},
	mounted: function () {
		this.video = this.$refs.video;
		if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
			navigator.mediaDevices
				.getUserMedia({ video: true, audio: false })
				.then((stream) => {
					console.log(stream);
					video.srcObject = stream;
					this.video.play();
				});
		}
	},
	methods: {
		capture() {
			this.canvas = this.$refs.canvas;
			var context = this.canvas
				.getContext("2d")
				.drawImage(this.video, 0, 0, 640, 480);
			this.captures.push(canvas.toDataURL("image/png"));
		}
	}
};
</script>