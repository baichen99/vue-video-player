<template>
  <div id="app">
    <VideoPlayer :src="src"></VideoPlayer>
  </div>
</template>

<script>
import VideoPlayer from './components/VideoPlayer.vue'
import videoTest from './assets/test.mp4'
export default {
  name: 'app',
  data() {
    return {
      src: videoTest
    }
  },
  components: {
    VideoPlayer
  }
}
</script>

<style>
#app {
  display: flex;
  justify-content: center;
}
</style>
