<template>
  <div>
    <video
      ref="videoPlayer"
      class="video-js vjs-theme-city vjs-16-9"
      @timeupdate="onPlayerTimeupdate($event)"
    />
  </div>
</template>

<script>
//import VideoPlayer from "@/components/VideoPlayer.vue";
//import store from "@/store";

//import Vue from "vue";
import videojs from "video.js";
import "video.js/dist/video-js.css";
// There are the theme that can be change for the video.
// City
import "@videojs/themes/dist/city/index.css";

// Fantasy
import "@videojs/themes/dist/fantasy/index.css";

// Forest
import "@videojs/themes/dist/forest/index.css";

// Sea
import "@videojs/themes/dist/sea/index.css";
import Vue from "vue";
export default {
  name: "BoxVideo",
  props: {
    videoId: Number,
    options: {
      type: Object,
      default() {
        return {};
      },
    },
  },
  async mounted() {
    let formData = new FormData();
    formData.append("videoId", this.videoId.toString());
    let responseTime = await Vue.axios.post("/api/timestamp/get", formData);
    this.player = videojs(
      this.$refs.videoPlayer,
      this.options,
      function onPlayerReady() {
        this.currentTime(responseTime.data.timestamp);
      }
    );
  },
  beforeDestroy() {
    if (this.player) {
      this.player.dispose();
    }
  },
  data: () => ({
    player: null,
  }),
  methods: {
    // eslint-disable-next-line no-unused-vars
    async onPlayerTimeupdate(player) {
      let formData = new FormData();
      formData.append("videoId", this.videoId.toString());
      formData.append("timestamp", this.player.currentTime());
      await Vue.axios.post("/api/timestamp/update", formData);
    },
  },
};
</script>
