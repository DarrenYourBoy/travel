<template>
  <div class="container">
    <el-carousel :interval="5000" class="el-carousel__container">
      <el-carousel-item v-for="(item,index) in banner" :key="index">
        <div
          :style="`
                background:url(${$axios.defaults.baseURL}${item.url}) center center no-repeat;
                background-size:contain contain;
                `"
          class="banner-image"
        ></div>
      </el-carousel-item>
    </el-carousel>
  </div>
</template>

<script>
export default {
  data() {
    return {
      banner: []
    };
  },
  mounted() {
    this.$axios({
      url: "/scenics/banners"
    }).then(res => {
      const { data } = res.data;
      this.banner = data;
    });
  }
};
</script>

<style scoped lang="less">
.el-carousel__item h3 {
  color: #475669;
  font-size: 14px;
  opacity: 0.75;
  line-height: 150px;
  margin: 0;
}

.el-carousel__item:nth-child(2n) {
  background-color: #99a9bf;
}

.el-carousel__item:nth-child(2n + 1) {
  background-color: #d3dce6;
}

.container{
    min-width:1000px;
    margin:0 auto;
    position:relative;

    /deep/ .el-carousel__container{
        height:700px;
    }

    .banner-image{
        width:100%;
        height:100%;
    }
}
</style>