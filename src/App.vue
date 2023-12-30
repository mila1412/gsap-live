<script setup>
import { ref, onMounted, computed } from 'vue';
import gsap from 'gsap';
const logo = ref(null);
const myVideo = ref(null);
const live = ref(null);
const time = ref(0);
setInterval(() => {
  time.value++;
}, 1000);
const timeLabel = computed(() => {
  let second = time.value % 60;
  let minute = Math.floor(time.value / 60) % 60;
  let hour = Math.floor(time.value / 3600) % 24;
  let pd = (num) => (num + '').padStart(2, '0');

  return `${pd(hour)}:${pd(minute)}:${pd(second)}`;
});

onMounted(() => {
  // gsap.to(logo.value, 1, {
  //   y: 200,
  //   rotate: 360,
  //   yoyo: true,
  //   repeat: 2
  // });

  //with timelines
  // var tl = gsap.timeline({ repeat: 2, repeatDelay: 1 });
  // tl.to(logo.value, { x: 100, duration: 1 });
  // tl.to(logo.value, { y: 50, duration: 1 });
  // tl.to(logo.value, { opacity: 0, duration: 1 });

  // gsap.to(live.value, 1, {
  //   css: {
  //     backgroundColor: 'black'
  //   },
  //   repeat: -1,
  //   yoyo: true,
  //   ease: 'none'
  // });

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
});
</script>

<template>
  <div class="video-container">
    <div class="live-label">
      <div class="red" ref="live">LIVE</div>
      <div class="counter">{{ timeLabel }}</div>
    </div>
    <video src="" ref="myVideo" autoplay="true"></video>
  </div>
  <!-- <img ref="logo" alt="Vue logo" src="./assets/logo.svg" width="125" height="125" /> -->
</template>

<style lang="scss">
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
  position: relative;
  .live-label {
    position: absolute;
    top: 30px;
    display: flex;
    .red {
      background-color: red;
      color: #fff;
      padding: 5px 10px;
      font-weight: 900;
    }
    .counter {
      background-color: #333;
      color: #fff;
      padding: 5px 10px;
    }
  }
}
</style>
