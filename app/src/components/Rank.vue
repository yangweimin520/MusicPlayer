<template>
  <div class="rank">
    <ul>
      <li
        class="rank-item"
        v-for="(item, index) in topList"
        :key="index"
        @click="showRank(item.id)"
      >
        <div class="rank-media">
          <img :src="item.picUrl">
          <span class="listen-count">{{item.listenCount | listenCount}}</span>
        </div>
        <div class="rank-info">
          <p class="rank-title">{{item.topTitle}}</p>
          <p class="rank-songs" v-for="(song, index) in item.songList" :key="index">
            {{index + 1}}
            {{song.songname}}
            <span
              class="rank-singername"
            >-{{song.singername}}</span>
          </p>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import RankPage from './RankPage'

export default {
  components: {
    RankPage
  },
  data() {
    return {
      topList: null
    };
  },
  methods: {
    showRank: function(id) {
      this.$router.push({ name: "rank", params: { id: id } });
    }
  },
  created: function() {
    this.$store.dispatch("getRankList").then(response => {
      this.topList = response.data.data.topList;
    });
  },
  filters: {
    listenCount: num => {
      return Math.round(num / 1000) / 10 + "万";
    }
  }
};
</script>

<style lang="less" scoped>
.rank {
  width: 100%;
  ul {
    margin-top: 60px;
    padding: 10px;
    margin-bottom: 50px;
    display: flex;
    flex-direction: column;
    list-style: none;
  }
  &-item {
    display: flex;
    height: 100px;
    background: #fff;
    margin-bottom: 10px;
    box-shadow: 0 0 10px #ddd;
    border-radius: 5px;
    overflow: hidden;
    .rank-media {
      width: 100px;
      height: 100px;
      position: relative;
      img {
        width: 100px;
        height: 100px;
        border-bottom-left-radius: 5px;
        border-top-left-radius: 5px;
        z-index: 0;
      }
      .listen-count {
        position: absolute;
        bottom: 3px;
        left: 5px;
        color: #fff;
        font-size: 12px;
      }
    }
    .rank-info {
      padding: 5px 10px;
      display: flex;
      flex-direction: column;
      justify-content: space-around;
      overflow: hidden;
      .rank-title {
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
      }
      .rank-songs {
        font-size: 14px;
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
      }
      .rank-singername {
        color: #8f8f8f;
      }
    }
  }
}
</style>
