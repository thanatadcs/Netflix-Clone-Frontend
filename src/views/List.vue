<template>
  <div>
    <div>
      <h1 class="page_title ml-14">My List</h1>
    </div>
    <v-container>
      <div class="ma-16 pl-16 ml-16" v-if="this.list.length === 0">
        <h1 class="ml-lg-16 pl-16">
          You haven't added any titles to your list yet.
        </h1>
      </div>
      <ul>
        <v-row>
          <v-col
            cols="12"
            sm="3"
            md="4"
            v-for="(video, index) in list"
            :key="index"
          >
            <div>
              <v-card
                class="mx-auto"
                width="344"
                max-height="360"
                :ripple="true"
                @mouseenter="setMouseHoverEnter(index)"
                @mouseleave="setMouseHoverLeave(index)"
              >
                <!-- Video Link -->
                <router-link class="routerLink" :to="'/video/' + video.id">
                  <div @click="spinner = true">
                    <h2 class="ml-2">{{ video.title }}</h2>
                    <v-img
                      lazy-src="https://i.imgur.com/XJRowdx.png"
                      height="200px"
                      :src="video.thumbnail"
                      v-show="!mouseHover[index]"
                    />
                    <!-- Box Video Stuff -->
                    <BoxVideo
                      :options="setVideoSrc(video.link)"
                      v-if="mouseHover[index]"
                      :video-id="video.id"
                    />
                    <!--                  <v-card-title class="justify-center">-->
                    <!--                    {{ video.title }}-->
                    <!--                  </v-card-title>-->
                  </div>
                </router-link>

                <!-- Drop Down -->
                <v-card-actions>
                  <v-spacer />
                  <v-btn icon @click="setDescription(index)">
                    <v-icon>{{
                      show.at(index) ? "mdi-chevron-up" : "mdi-chevron-down"
                    }}</v-icon>
                  </v-btn>
                </v-card-actions>

                <v-expand-transition>
                  <div v-show="show.at(index)">
                    <v-card-text>
                      {{ video.description }}
                    </v-card-text>
                  </div>
                </v-expand-transition>
              </v-card>
            </div>
          </v-col>
        </v-row>
      </ul>
      <v-progress-circular
        class="spinner"
        indeterminate
        color="primary"
        v-if="spinner"
      />
    </v-container>
  </div>
</template>

<script>
import store from "@/store";
import BoxVideo from "@/views/BoxVideo";

export default {
  name: "Home.vue",
  components: {
    BoxVideo,
  },
  async created() {
    await store.dispatch("getAllListVideos");
    for (let i = 0; i < store.state.list.length; i++) {
      this.$set(this.list, i, store.state.list.at(i));
    }
  },
  data() {
    return {
      list: [],
      search: "",
      videos: store.state.videos,
      spinner: false,
      show: new Array(store.state.numVideos).fill(false),
      mouseHover: new Array(store.state.numVideos).fill(false),
      videoOptions: {
        autoplay: true,
        muted: true,
        controls: false,
        sources: [
          {
            src: "", // link to the video!!!
            type: "application/x-mpegurl",
          },
        ],
      },
    };
  },
  methods: {
    setDescription(index) {
      this.$set(this.show, index, !this.show[index]);
    },
    setMouseHoverEnter(index) {
      this.$set(this.mouseHover, index, true);
    },
    setMouseHoverLeave(index) {
      this.$set(this.mouseHover, index, false);
    },
    setVideoSrc(videoLink) {
      this.$set(this.videoOptions.sources[0], "src", videoLink);
      return this.videoOptions;
    },
    setAddList(index) {
      this.$set(this.add, index, !this.add.at(index));
    },
  },
  computed: {
    filteredVideos() {
      return this.videos.filter((video) =>
        video.title.toLowerCase().includes(this.search.toLowerCase())
      );
    },
  },
};
</script>
<style lang="scss" scoped>
.routerLink {
  text-decoration: none;
}
.spinner {
  top: 90%;
  left: 90%;
  position: absolute;
}
body {
  background: #f6e9e3;
  background-size: 100% 100%;
}
</style>
