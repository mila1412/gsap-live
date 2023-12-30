<script setup>
import { ref, onMounted } from 'vue';
import gsap from 'gsap';
const logo = ref(null);
onMounted(() => {
  // gsap.to(logo.value, 1, {
  //   y: 200,
  //   rotate: 360,
  //   yoyo: true,
  //   repeat: 2
  // });

  //WITH Timelines
  var tl = gsap.timeline({ repeat: 2, repeatDelay: 1 });
  tl.to(logo.value, { x: 100, duration: 1 });
  tl.to(logo.value, { y: 50, duration: 1 });
  tl.to(logo.value, { opacity: 0, duration: 1 });
});
</script>

<template>
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
</style>
