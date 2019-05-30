<template>
  <div id="play" class="music-play-page">
    <div class="music-album">
      <div class="play-page-hide-btn" @touchend.prevent.stop="hidePlayPage" @click="hidePlayPage">
        <img src="../assets/icon-jiantou.png" alt>
      </div>
      <img class="coverImg" :src="coverImgUrl" @touchstart="movestart" @tochend="moveend">
    </div>
    <div class="button-group">
      <img class="blurbg" :src="coverImgUrl">
      <div class="progress-bar-group">
        <div class="progress-bar">
          <div class="progress" :style="{width:indicatorPosition+'%'}"></div>
          <div class="indicater" :style="{left:indicatorPosition+'%'}"></div>
        </div>
        <div class="time-indicater">
          <span>{{currentTime}}</span>
          <span>{{duration}}</span>
        </div>
      </div>
      <div class="music-info">
        <p class="music-name">{{ song.name }}</p>
        <p class="music-author">{{ song.singer | singer }}</p>
      </div>
      <div class="lyric">
        <lyric :songid="song.id" :currentTime="currentTime"></lyric>
      </div>
      <div class="music-ctrl">
        <ul>
          <li>
            <img src="../assets/icon-like.png" alt>
          </li>
          <li>
            <img
              src="../assets/icon-shangyiqu.png"
              @touchend.prevent="playFront"
              @click="playFront"
            >
          </li>
          <li>
            <img
              :src="playing ? $parent.iconPause : $parent.iconPlay"
              @click="$parent.tapButton"
              @touchend="$parent.tapButton"
            >
          </li>
          <li>
            <img src="../assets/icon-xiayiqu.png" @touchend.prevent="playNext" @click="playNext">
          </li>
          <li>
            <img
              src="../assets/icon-list.png"
              @touchend.prevent="showPlayList"
              @click="showPlayList"
            >
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import { mapMutations, mapState, mapGetters } from "vuex";
import Lyric from "./Lyric.vue";

export default {
  components: {
    Lyric
  },
  data() {
    return {
      clientY: 0
    };
  },
  methods: {
    hidePlayPage: function() {
      this.$parent.playPageShow = false;
    },
    movestart: function() {
      console.log("start" + event.touches[0].clientY);
    },
    moveend: function(event) {
      if (event.changedTouches[0].clientY - this.clientY > 0) {
        this.hidePlayPage();
      }
    },
    showPlayList: function() {
      this.$store.commit("showPlayingList");
    },
    ...mapMutations(["play", "pause", "playFront", "playNext"])
  },
  computed: {
    ...mapGetters(["currentTime", "duration", "coverImgUrl"]),
    ...mapState({
      indicatorPosition: state =>
        (state.PlayService.currentTime / state.PlayService.duration) * 100,
      playing: state => state.PlayService.playing,
      song: state => state.PlayService.song
    })
  },
  filters: {
    singer: val => {
      if (typeof val === "string") {
        return val;
      } else if (val instanceof Array) {
        let singer = "";
        val.forEach(item => {
          singer = singer + item.name + " ";
        });
        return singer;
      }
    }
  }
};
</script>

<style lang="less">
.music-play-page {
  width: 100%;
  max-width: 68vh;
  height: 100%;
  position: fixed;
  top: 0;
  z-index: 5;
  .music-album {
    width: 100%;
    height: 100vw;
    max-height: 68vh;
    .play-page-hide-btn {
      width: 25px;
      height: 25px;
      top: 27px;
      left: 11px;
      position: absolute;
      border-radius: 50%;
      background: hsla(0, 0%, 100%, 0.8);
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 1;
      img {
        width: 12px;
        height: 12px;
      }
    }
    .coverImg {
      width: 100%;
      display: block;
    }
  }
  .button-group {
    position: absolute;
    width: 100%;
    left: 0;
    bottom: 0;
    top: 100vw;
    background: hsla(0, 0%, 100%, 0.76);
    overflow: hidden;
    display: flex;
    flex-direction: column;
    >img {
      position: absolute;
      width: 100%;
      z-index: -1;
      display: block;
      filter: blur(30px);
    }
    .progress-bar-group {
      height: 30px;
      .progress-bar {
        height: 4px;
        background: #ccc;
        position: relative;
        .progress {
          height: 100%;
          background: #7f7f7f;
        }
        .indicater {
          position: absolute;
          width: 2px;
          height: 8px;
          top: 0;
          background: #ff2d55;
        }
      }
      .time-indicater {
        margin-top: 8px;
        line-height: 16px;
        font-size: 12px;
        display: flex;
        justify-content: space-between;
        span {
          padding: 0 10px;
        }
      }
    }
    .music-info {
      .music-name {
        text-align: center;
        font-weight: 700;
        font-size: 18px;
      }
      .music-author {
        text-align: center;
        font-size: 12px;
        color: #8f8f8f;
      }
    }
    .lyric {
      min-height: 35px;
      flex: 1;
      display: flex;
      flex-direction: column;
    }
    .music-ctrl {
      height: 50px;
      ul {
        height: 100%;
        width: 100%;
        list-style: none;
        li {
          float: left;
          width: 20%;
          height: 100%;
          img {
            display: block;
            width: 35px;
            margin: 0 auto;
          }
        }
        li:first-child img {
          margin-left: 10px;
        }
        li:last-child img {
          margin-right: 10px;
        }
      }
    }
  }
}
</style>
