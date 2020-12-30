<template>
  <VideoPlayer
    src-url="https://cdn.videvo.net/videvo_files/video/premium/video0243/small_watermarked/02_fireplace_Denoised_20_static_fireplace_preview.webm"
    :autoplay="true"
  />
</template>

<script>
import VideoPlayer from "./components/VideoPlayer.vue"
export default {
  name: "App",
  components: {
    VideoPlayer,
  },
}
</script>
