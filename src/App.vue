<script setup>
import { ref, onMounted } from 'vue';
import gsap from 'gsap';
const logo = ref(null);
const myVideo = ref(null);
onMounted(() => {
  // gsap.to(logo.value, 1, {
  //   y: 200,
  //   rotate: 360,
  //   yoyo: true,
  //   repeat: 2
  // });

  //with timelines
  var tl = gsap.timeline({ repeat: 2, repeatDelay: 1 });
  tl.to(logo.value, { x: 100, duration: 1 });
  tl.to(logo.value, { y: 50, duration: 1 });
  tl.to(logo.value, { opacity: 0, duration: 1 });

  async function getMedia(constraints) {
    let stream = null;
    try {
      stream = await navigator.mediaDevices.getUserMedia(constraints);
      var video = myVideo.value;
      video.srcObject = stream;
      video.onloadedmetadata = function () {
        video.play();
      };
      myVideo.value.srcObject = stream;
    } catch (err) {
      console.log(err.name + ': ' + err.message);
    }
  }
  var constraints = { audio: true, video: { width: 1280, height: 720 } };
  getMedia(constraints);

  // promise
  // var constraints = { audio: true, video: { width: 1280, height: 720 } };
  // navigator.mediaDevices
  //   .getUserMedia(constraints)
  //   .then((mediaStream) => {
  //     var video = myVideo.value;
  //     video.srcObject = mediaStream;
  //     video.onloadedmetadata = function () {
  //       video.play();
  //     };
  //   })
  //   .catch((err) => {
  //     console.log(err.name + ': ' + err.message);
  //   });
});
</script>

<template>
  <div class="video-container">
    <video src="" ref="myVideo" autoplay="true"></video>
  </div>
  <img ref="logo" alt="Vue logo" src="./assets/logo.svg" width="125" height="125" />
</template>

<style>
html,
body {
  background-color: #333;
  display: flex;
  justify-content: center;
  align-content: center;
}
#app {
  width: 390px;
  height: 744px;
  text-align: center;
  margin-top: 60px;
  background-color: #fff;
}
.video-container {
  display: flex;
  justify-content: center;
  height: 450px;
  overflow: hidden;
}
</style>
