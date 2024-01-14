<script setup>
import { ref, onMounted, computed, reactive, nextTick } from 'vue';
import gsap from 'gsap';
const logo = ref(null);
const myVideo = ref(null);
const live = ref(null);

onMounted(() => {
  // gsap.to(logo.value, 1, {
  //   y: 200,
  //   rotate: 360,
  //   yoyo: true,
  //   repeat: 2
  // });

  //with timelines
  // let tl = gsap.timeline({ repeat: 2, repeatDelay: 1 });
  // tl.to(logo.value, { x: 100, duration: 1 });
  // tl.to(logo.value, { y: 50, duration: 1 });
  // tl.to(logo.value, { opacity: 0, duration: 1 });

  gsap.to(live.value, 1, {
    css: {
      backgroundColor: 'black'
    },
    repeat: -1,
    yoyo: true,
    ease: 'none'
  });

  // promise
  // let constraints = { audio: true, video: { width: 1280, height: 720 } };
  // navigator.mediaDevices
  //   .getUserMedia(constraints)
  //   .then((mediaStream) => {
  //     let video = myVideo.value;
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
      let video = myVideo.value;
      video.srcObject = stream;
      video.onloadedmetadata = function () {
        video.play();
      };
      myVideo.value.srcObject = stream;
    } catch (err) {
      console.log(err.name + ': ' + err.message);
    }
  }
  let constraints = { audio: true, video: { width: 1280, height: 720 } };
  getMedia(constraints);
});

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

const emojiList = reactive(['😊', '😂', '😍', '❤', '😢']);
const currentList = reactive([]);
const addEmoji = (emoji) => {
  currentList.push(emoji);
  nextTick(() => {
    let tl = gsap.timeline();
    let _id = `.emoji_${currentList.length - 1}`;
    tl.set(_id, { scale: 0.2, x: Math.random() * -100 + 20 });
    tl.to(_id, 1, {
      y: -200 + Math.random() * -100,
      scale: 1,
      ease: 'power4.out'
    }).to(_id, 2, {
      x: -500,
      scale: 0.8,
      ease: 'power1.in'
    });
  });
};

let objCopy = {
  name: 'Mila',
  content: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit.',
  id: 0
};
const message = ref('');
const commentList = reactive([]);
const addComment = () => {
  // 重新拷貝一份轉換為個別物件
  // objCopy = JSON.parse(JSON.stringify(objCopy));
  objCopy = { ...objCopy };
  if (message.value) {
    objCopy.content = message.value;
    objCopy.id++;
    commentList.push(objCopy);
  }
  message.value = '';
};
const deleteComment = (id) => {
  let removeId = commentList.findIndex((comment) => comment.id === id);
  commentList.splice(removeId, 1);

  // 也可以用 filter，但是 reactive 無法整包替換，要改用 ref
  // commentList.value = commentList.value.filter((comment) => comment.id !== id);
};
</script>

<template>
  <div class="video-container">
    <div class="live-label">
      <div class="red" ref="live">LIVE</div>
      <div class="counter">{{ timeLabel }}</div>
    </div>
    <video src="" ref="myVideo" autoplay="true"></video>
    <ul class="emoji-list">
      <li v-for="(emoji, index) in emojiList" :key="index" @click="addEmoji(emoji)">
        {{ emoji }}
      </li>
    </ul>
  </div>
  <div class="comment-container">
    <div class="comment-btn">
      <input type="text" v-model="message" @keyup.enter="addComment" />
      <button class="add-btn" @click="addComment">Add Comment</button>
    </div>
    <TransitionGroup name="fade">
      <div class="comment-list" v-for="comment in commentList" :key="comment.id">
        <div class="head"></div>
        <div class="content">
          <div class="name">{{ comment.name + comment.id }}</div>
          <div class="message">{{ comment.content }}</div>
        </div>
        <button class="btn" @click="deleteComment(comment.id)">delete</button>
      </div>
    </TransitionGroup>

    <ul class="current-list">
      <li v-for="(emoji, index) in currentList" :key="index" :class="`emoji_${index}`">
        {{ emoji }}
      </li>
    </ul>
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
  text-align: center;
  margin-top: 60px;
  background-color: #fff;
  position: relative;
  overflow-x: hidden;
}
.video-container {
  display: flex;
  justify-content: center;
  height: 450px;
  overflow: hidden;
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
  .emoji-list {
    position: absolute;
    bottom: 0;
    right: 0;
    display: flex;
    list-style: none;
    li {
      width: 50px;
      font-size: 30px;
      cursor: pointer;
      // 去程(點下去) & 回程(放開)所需時間
      transition: 1s;
      &:active {
        // 去程動畫所需時間，沒設會跟上面的 transition 一樣秒數
        transition: 0s;
        transform: scale(0.8);
      }
    }
  }
}
.comment-container {
  position: relative;
  overflow-y: auto;
  height: 300px;
  .comment-btn {
    padding-top: 10px;
  }
  .comment-list {
    display: flex;
    align-items: center;
    .head {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      background-color: #000;
      margin: 10px;
    }
    .content {
      font-size: 14px;
      flex: 1;
      text-align: left;
      .name {
        margin-bottom: 5px;
      }
    }
    .btn {
      margin: 10px;
    }

    &.fade-enter-active,
    &.fade-leave-active {
      transition: all 1s;
    }

    &.fade-enter-from,
    &.fade-leave-to {
      opacity: 0;
      transform: translateY(10px);
    }

    &.fade-enter-to,
    &.fade-leave-from {
      opacity: 1;
      transform: translateY(0px);
    }
  }
  .current-list li {
    position: absolute;
    top: 50px;
    right: 50px;
    list-style: none;
    font-size: 30px;
  }
}
</style>
