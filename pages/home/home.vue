<template>
  <view>
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item, index) in swiperList" :key="index">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id='+ item.goods_id">
          <img :src="item.image_src" alt="">
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 分类导航区域-->
    <view class="nav-list">
      <view class="nav-item" v-for="(item, i) in navList" :key="i" @click="navClick(item)">
        <image :src="item.image_src" class="nav-img"></image>
      </view>
    </view>
    <!-- 楼层区域-->
    <view class="floor-list">
      <view class="floor-item" v-for="(item, index) in floorList" :key="index">
        <image :src="item.floor_title.image_src" class="floor-title"></image>
        <!-- 楼层图片-->
        <view class="floor-img-box">
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
          </navigator>
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2, index2) in item.product_list" :key="index2" v-if="index2 !== 0" :url="item2.url">
              <image :src="item2.image_src" mode="widthFix" :style="{width: item2.image_width + 'rpx'}"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        swiperList:[],
        navList:[], //分类导航数据列表
        floorList:[]
      };
    },
    onLoad() {
      this.getSwiperList()
      this.getNavList()
      this.getFloorList()
    },
    methods:{
      async getFloorList(){
        const res = await uni.$http.get('/api/public/v1/home/floordata')
        if(res.data.meta.status !== 200) return uni.$showMsg()
        res.data.message.forEach(floor => {
          floor.product_list.forEach(item => {
            item.url = '/subpkg/goods_list/goods_list?' + item.navigator_url.split('?')[1]
          })
        })
        this.floorList = res.data.message
        uni.$showMsg('数据请求成功')
      },
      async getNavList(){
        const res = await uni.$http.get('/api/public/v1/home/catitems')
        if(res.data.meta.status !== 200) return uni.$showMsg()
        this.navList = res.data.message
        uni.$showMsg('数据请求成功')
      },
      async getSwiperList(){
        const res = await uni.$http.get('/api/public/v1/home/swiperdata')
        if(res.data.meta.status !== 200) return uni.$showMsg()
        this.swiperList = res.data.message
        uni.$showMsg('数据请求成功')
      },
      navClick(item) {
        if(item.name === '分类'){
          uni.switchTab({
            url: '/pages/cate/cate'
          })
        }
      }
    }
  }
</script>

<style lang="scss">
  swiper {
   height: 330rpx;
   .swiper-item,
   image {
     width: 100%;
     height: 100%;
   }
  }
  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 30rpx 0;
    .nav-img{
      width: 128rpx;
      height: 140rpx;
    }
  }
  .floor-title {
    height: 60rpx;
    width: 100%;
    display: flex;
  }
  .floor-img-box{
    display: flex;
    padding-left: 10rpx;
    .right-img-box{
      display: flex;
      flex-wrap: wrap;
      justify-content: space-around;
    }
  }
</style>
