<template>
  <div id="lyric">
    <p class="lyric-item" v-for="(item, index) in currentLyric" :key="index">{{item}}</p>
  </div>
</template>

<script>
import Base64 from "../utils/base64";
export default {
  props: ["currentTime", "songid"],
  data() {
    return {
      lyric: null
    };
  },
  components: {},
  methods: {},
  computed: {
    currentLyric: function() {
      if (this.lyric !== null) {
        let that = this;
        let pastLyric = [];
        let i = 0;
        Object.keys(this.lyric).forEach(function(key) {
          if (
            key
              .split(":")
              .reduce(
                (a, b) =>
                  parseInt(a) * 60 * 100 +
                  b.split(".").reduce((a, b) => parseInt(a) * 100 + parseInt(b))
              ) -
              120 <=
            that.currentTimeStamp
          ) {
            if (that.lyric[key] !== "\n") pastLyric.push(that.lyric[key]);
          } else if (i <= 1 && that.lyric[key] != "\n") {
            pastLyric.push(that.lyric[key]);
            i++;
          }
        });
        return pastLyric.slice(pastLyric.length - 4, pastLyric.length - 1);
      }
    },
    currentTimeStamp: function() {
      let t = this.currentTime.split(":");
      return (parseInt(t[0]) * 60 + parseInt(t[1])) * 100;
    }
  },
  watch: {
    songid: function(id) {
      this.$store.dispatch("getLyric", id).then(response => {
        const lyr = Base64.decode(response.data.lyric);
        this.lyric = Base64.decode(response.data.lyric)
          .split("[")
          .slice(5)
          .map(str => {
            let t = str.split("]");
            return { [t[0]]: t[1] };
          })
          .reduce((a, b) => {
            return { ...a, ...b };
          });
      });
    }
  }
};
</script>

<style lang="less" scoped>
#lyric {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  height: 100%;
  flex-grow: 1;
}
.lyric-item {
  text-align: center;
}
.lyric-item {
  :first-child,
  :last-child {
    font-size: 14px;
    color: #4d4d4d;
  }
}
</style>
