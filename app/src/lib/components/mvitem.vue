<template>
  <div class="mvitem" @click="openmv">
    <div class="img-wrapper">
      <img alt="name" class="mv-img" v-lazy="imgurl">
    </div>
    <div class="mv-info">
      <p class="name">{{name}}</p>
      <p class="author">{{author}}</p>
      <p class="date">发行时间:{{date}}</p>
      <div class="mv-playcount">
        <span>播放量:{{playcount | listenCount}}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['name', 'author', 'playcount', 'date', 'vid'],
  components: {},
  data () {
    return {}
  },
  computed: {
    imgurl () {
      return "https://shp.qpic.cn/qqvideo_ori/0/" + this.vid + "_360_204/0" 
    }
  },
  methods: {
    openmv() {
      window.open("https://y.qq.com/portal/mv/v/" + this.vid + ".html")
    }
  },
  filters: {
    listenCount: num => Math.round(num / 1000) / 10 + '万'
  }
}

</script>

<style lang="less" scoped>
.mvitem {
  display: flex;
  background: #fff;
  padding: 5px;
  overflow: hidden;
  margin: 5px 10 px;
  box-shadow: 0 0 10px #ddd;
  .img-wrapper {
    img {
      height: 33vw;
      max-height: 150px;
      display: block;
      border-radius: 5px;
    }
  }
  .mv-info {
    position: relative;
    padding: 5px;
    font-weight: 300;
    font-size: 14px;
    color: #777;
    .author, .date {
      line-height: 25px;
    }
    .mv-playcount {
      position: absolute;
      bottom: 5px;
      left: 5px;
      font-size: 14px;
      display: flex;
      align-items: center;
    }
  }
}
</style>
