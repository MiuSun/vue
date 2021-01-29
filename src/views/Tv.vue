<template>
  <div>
    <ul class="tv-list">
      <li class="tv-item" v-for="item in tvList" :key="item.id" @click="getDetail(item.id)">
        <div class="img">
          <img :src="'https://images.weserv.nl/?url=' + item.cover.url" alt />
        </div>
        <div class="content">
          <h3>{{item.title}}</h3>
          <p>{{item.info}}</p>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tvList: [],
      start: 0,
      isFinish: true,
    };
  },
  created() {
    this.getData();
  },
  mounted() {
    this.listenScroll();
  },
  methods: {
    
    getData() {

      if (this.isFinish) {
        this.isFinish = false;
        let baseUrl = "https://bird.ioliu.cn/v2?url=";
        let tvUrl = `https://m.douban.com/rexxar/api/v2/subject_collection/tv_domestic/items?os=ios&for_mobile=1&start=${this.start}&count=10`;
        this.axios
          .get(baseUrl + tvUrl)
          .then((res) => {
            this.tvList = this.tvList.concat(res.data.subject_collection_items);
            this.isFinish = true;
          })
          .catch((err) => console.log(err));
      }
    },
    listenScroll() {
      let htmlDom = document.documentElement;
      let deviceHeight = htmlDom.clientHeight;
      let fullHeight = 0;
      let scrollTop = 0;
      window.onscroll = () => {
        fullHeight = htmlDom.offsetHeight; 
        scrollTop = htmlDom.scrollTop; 
        if (scrollTop + deviceHeight > fullHeight - 20) {
          if (this.isFinish) {
            this.start += 10;
          }
          if (this.start < 50) {
            this.getData();
          }
          console.log("滚动到底部");
        }
      };
    },
    getDetail(id) {
      this.$router.push("/tvdetails/" + id);
    },
  },
};
</script>

<style lang="scss" scoped>
.tv-list {
  .tv-item {
    display: flex;
    padding: 10px;
    border-bottom: 1px solid #cccccc;
    .img {
      flex: 3;
      img {
        width: 100%;
      }
    }
    .content {
      flex: 5;
      padding: 10px;
      h3 {
        font-weight: bolder;
        margin-bottom: 20px;
      }
    }
  }
}
</style>