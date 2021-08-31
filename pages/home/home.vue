<template>
  <view>
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="2000" :duration="1000" circular>
      <swiper-item  v-for="(item,i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
   <image :src="item.image_src"></image>
      </navigator>
      </swiper-item>
    </swiper>
  <!-- 分类导航区域 -->
  <view class="nav-list">
    <view class="nav-item" v-for="(item, i) in navList" :key="i" @click="navClickHandler(item)">
      <image :src="item.image_src"></image>
    </view>
  </view>
  <!-- 楼层区 -->
  <view class="floor-list">
    <view class="floor-item" v-for="(item,i) in  floorList" :key="i">
      <image class="floor-title" :src="item.floor_title.image_src"></image>
      <view class="floor-img-box">
        <navigator class="left-img-box" :url="item.product_list[0].url">
          <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
        </navigator>
         <view class="right-img-box">
           <navigator :url="item2.url" class="right-img-item" v-for="(item2, i2) in item.product_list" :key="i2" v-if="i2 !== 0">
                 <image :src="item2.image_src" mode="widthFix" :style="{width: item2.image_width + 'rpx'}"></image>
               </navigator>
         </view>
      </view>
    </view>
  </view>
  
  </view>

</template>
<script>
  import badgeMix from '@/mixins/tabbar-badge.js'
  export default {
    data() {
      return {
        //轮播图的数据列表
        swiperList:[],
        //数据导航的分类列表
        navList:[],
         // 楼层的数据列表
              floorList: [],
      };
    },
            mixins: [badgeMix],
    onLoad() {
      this.getSwiperList()
      this.getNavList()
          this.getFloorList()
    },
    methods:{
      async getSwiperList(){
      const {data:res} =await uni.$http.get('/api/public/v1/home/swiperdata')
      if (res.meta.status !== 200) return uni.$showMsg()
            // 3.3 请求成功，为 data 中的数据赋值
            this.swiperList = res.message
             uni.$showMsg('数据请求成功！')
      },
      async getNavList(){
        const { data: res } = await uni.$http.get('/api/public/v1/home/catitems')
              if (res.meta.status !== 200) return uni.$showMsg()
              this.navList = res.message
      },
      navClickHandler(item){
         if (item.name === '分类') {
            uni.switchTab({
              url: '/pages/cate/cate'
            })
          }
      },
      async getFloorList() {
            const { data: res } = await uni.$http.get('/api/public/v1/home/floordata')
            if (res.meta.status !== 200) return uni.$showMsg()
            // 通过双层 forEach 循环，处理 URL 地址
              res.message.forEach(floor => {
                floor.product_list.forEach(prod => {
                  prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
                })
              })
            this.floorList = res.message
          },
          gotoSearch() {
            uni.navigateTo({
              url: '/subpkg/search/search'
            })
          }

      
      
    }
  }
</script>

<style lang="scss">
swiper{
  height: 330rpx;
  .swiper-item,image{
    width: 100%;
     height: 100%;
  }
}
.nav-list{
  display: flex;
  justify-content: space-around;
    margin: 15px 0;
  .nav-item{

    image{
      width: 128rpx;
      height: 140rpx;
    }
  }
}
.floor-title{
  width: 100%;
  height: 60px;
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
.search-box {
  // 设置定位效果为“吸顶”
  position: sticky;
  // 吸顶的“位置”
  top: 0;
  // 提高层级，防止被轮播图覆盖
  z-index: 999;
}

</style>
