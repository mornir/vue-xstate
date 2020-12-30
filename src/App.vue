<template>
  <VideoPlayer
    src-url="https://cdn.videvo.net/videvo_files/video/premium/video0243/small_watermarked/02_fireplace_Denoised_20_static_fireplace_preview.webm"
    :autoplay="true"
  />
  <SearchDebounce />
</template>

<script>
import VideoPlayer from "./components/VideoPlayer.vue"
import SearchDebounce from "./components/SearchDebounce.vue"
export default {
  name: "App",
  components: {
    VideoPlayer,
    SearchDebounce,
  },
}
</script>
