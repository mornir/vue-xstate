<template>
  <video
    muted
    :autoplay="autoplay"
    playsinline
    ref="video"
    :src="srcUrl"
  ></video>
  <h1>State: {{ state.value }}</h1>
  <br />
  <button type="button" @click="send('PLAY')">Play</button>
  <button type="button" @click="send('PAUSE')">Pause</button>
</template>

<script>
import { ref } from "vue"

import { useMachine } from "@xstate/vue"
import { Machine } from "xstate"

const createMachine = (ref, options = { autoplay: false }) =>
  Machine(
    {
      id: "player",
      initial: "unknown",
      context: {
        video: ref,
        options,
      },
      states: {
        unknown: {
          always: [
            { target: "playing", cond: "shouldAutoplay" },
            { target: "paused" },
          ],
        },
        playing: {
          entry: "playVideo",
          on: { PAUSE: "paused" },
        },
        paused: {
          entry: "pauseVideo",
          on: { PLAY: "playing" },
        },
      },
    },
    {
      actions: {
        playVideo({ video }) {
          if (video.value) {
            video.value.play()
          }
        },
        pauseVideo({ video }) {
          if (video.value) {
            video.value.pause()
          }
        },
      },
      guards: {
        shouldAutoplay: ({ options }) => {
          return options.autoplay
        },
      },
    }
  )

export default {
  name: "VideoPlayer",
  props: {
    srcUrl: {
      type: String,
      required: true,
    },
    autoplay: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  setup({ autoplay }) {
    const video = ref(null)
    const playerMachine = createMachine(video, { autoplay })
    const { state, send } = useMachine(playerMachine)

    return {
      state,
      send,
      video,
    }
  },
}
</script>
