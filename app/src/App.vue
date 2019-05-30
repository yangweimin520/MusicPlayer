<template>
  <div id="app">
    <action-sheet></action-sheet>
    <transition :name="routerViewAnimation">
      <router-view v-show="!blurBgShow"></router-view>
    </transition>

    <search v-show="!blurBgShow" @searchshow="rankshow=false" @searchhide="rankshow=true"></search>
    <div class="content-wraper" v-show="rankshow&&!blurBgShow">
      <swiper class="swiper-box" :options="swiperOption">
        <swiper-slide class="swiper-item">
          <rank></rank>
        </swiper-slide>
        <swiper-slide class="swiper-item">
          <recommand></recommand>
        </swiper-slide>
        <div class="swiper-pagination" slot="pagination"></div>
      </swiper>
    </div>
    <transition name="play-slide" @after-enter="showBlurBg" @before-leave="hideBlurBg" @after-leave="routerViewAnimation='page-slide'">
      <play v-show="playPageShow"></play>
    </transition>

    <transition class="bar-slide">
      <div id="play-bar" v-show="!playPageShow">
        <div class="play-bar-image-container" @touchstart="showPlayPage" @click="showPlayPage">
          <img class="play-bar-image" v-lazy="coverImgUrl">
        </div>
        <p class="play-bar-text" @touchstart="showPlayPage" @click="showPlayPage">{{ song.name }}</p>
        <img
          class="play-bar-button"
          :src="playing?iconPause:iconPlay"
          @touchend="tapButton"
          @click="tapButton"
        >
      </div>
    </transition>
  </div>
</template>

<script>
import ActionSheet from "./components/ActionSheet";
import Search from "./components/Search";
import Rank from "./components/Rank";
import Recommand from "./components/Recommand";
import Play from './components/Play'

import { mapMutations, mapState, mapGetters } from "vuex";
import { swiper, swiperSlide } from "vue-awesome-swiper";

import "swiper/dist/css/swiper.css";

const TAB_NAME = ["排行榜", "推荐"];

export default {
  name: "App",
  components: {
    ActionSheet,
    Search,
    Rank,
    Recommand,
    Play,
    swiper,
    swiperSlide
  },
  data() {
    return {
      iconPlay: require("./assets/icon-play.png"),
      iconPause: require("./assets/icon-pause.png"),
      playPageShow: false,
      blurBgShow: false,
      rankshow: true,
      routerViewAnimation: "page-slide",
      swiperOption: {
        pagination: ".swiper-pagination",
        paginationClickable: true,
        paginationBulletRender(swiper, index, className) {
          return `<span class="${className} swiper-pagination-bullet-custom">${
            TAB_NAME[index]
          }</span>`;
        }
      }
    };
  },
  methods: {
    tapButton(event) {
      event.preventDefault();
      this.playing ? this.pause() : this.play();
    },
    showPlayPage(event) {
      event.preventDefault();
      this.playPageShow = true;
    },
    hidePlayPage(event) {
      event.preventDefault();
      this.playPageShow = false;
    },
    showBlurBg() {
      this.routerViewAnimation = "fade";
      this.blurBgShow = true;
    },
    hideBlurBg() {
      this.blurBgShow = false;
    },
    ...mapMutations(["play", "pause", "playContinue"])
  },
  computed: {
    ...mapGetters(["coverImgUrl"]),
    ...mapState({
      dataUrl: state =>
        `https://dl.stream.qqmusic.qq.com/C100${
          state.PlayService.song.mid
        }.m4a?fromtag=46`,
      playing: state => state.PlayService.playing,
      song: state => state.PlayService.song
    })
  }
};
</script>

<style lang="less">
@import "./styles/base.css";
</style>

<style lang="less">
html {
  overflow-x: hidden;
  background: #eeeeee;
}

body {
  display: flex;
  overflow-x: hidden;
}

#app {
  width: 100%;
  height: 100%;
}

.content-wraper {
  margin-top: 60px;
  .swiper-box {
    width: 100%;
    height: 100%;
    margin: 0 auto;
  }
  .swiper-item {
    height: 100%;
  }
}
.swiper-pagination {
  top: 0;
  height: 50px;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  width: 100%;
  .swiper-pagination-bullet-custom {
    width: 100% !important;
    height: 100% !important;
    margin: 0 !important;
    text-align: center;
    line-height: 50px;
    color: #999999;
    border-radius: 0 !important;
    background: #fff !important;
    opacity: 1 !important;
  }
}

.swiper-pagination-bullet-custom.swiper-pagination-bullet-active {
  color: #000;
}

#play-bar {
  position: fixed;
  bottom: 0;
  width: 100%;
  height: 50px;
  background: -webkit-linear-gradient(top, #f9f9f9, #f3f3f3);
  display: flex;
  justify-content: space-between;
  align-items: center;
  z-index: 2;
  .play-bar-image-container {
    width: 37.5px;
    height: 37.5px;
    padding-left: 15px;
  }
  .play-bar-image {
    width: 37.5px;
    height: 37.5px;
    border-radius: 5px;
    display: inline-block;
  }
  .play-bar-text {
    flex-grow: 1;
    padding-left: 10px;
  }
  .play-bar-button {
    width: 20px;
    height: 20px;
    padding-right: 15px;
  }
}
</style>
