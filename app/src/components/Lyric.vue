<template>
  <div id="lyric">
    <p class="lyric-item" v-for="(item, index) in currentLyric" :key="index">{{v}}</p>
  </div>
</template>

<script>
import Base64 from '../utils/base64'
export default {
  props: ['currentTime', 'songid'],
  data () {
    return {
      lyric: null
    }
  },
  components: {},
  methods: {},
  computed: {
    currentLyric: function() {
      if(this.lyric !== null) {
        let that = this;
        let pastLyric = [];
        let i = 0;
        Object.keys(this.lyric).forEach(function (key) {
          if(key.split(':')
            .reduce((a, b) => parseInt(a) * 60 * 100 + b.split('.').reduce((a, b) => parseInt(a) * 100 + parseInt(b))) -120 <= that.currentTimeStamp) {
              if(that.lyric[key] !== '\n') pastLyric.push(that.lyric[key])
            } else if(i <= 1 && that.lyric[key] != '\n') {
              pastLyric.push(that.lyric[key]);
              i++;
            }
        })
      }
      return pastLyric.slice(pastLyric.length - 4, pastLyric.length - 1);
    }
  },
  watch: {
    songid: function(id) {
      this.$store.dispatch('getLyric', id)
      .then((response) => {
        this.lyric = Base64
          .decode(response.data.lyric)
      })
    }
  }
}
</script>

<style lang="less" scoped>

</style>
