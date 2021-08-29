<template>
  <view v-if="goods_info.goods_name" class="goods-detail-container">
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" circular>
      <swiper-item v-if="item.pics_big" v-for="(item,i) in goods_info" :key="i">
       <image  v-if="item.pics_big"  :src="item.pics_big" @click="preview(i)"></image>  
      </swiper-item>
    </swiper> 
    <view class="goods-info-box">
      <view class="price">
        ￥{{goods_info.goods_price}} 
      </view>
      <view class="goods-name">{{goods_info.goods_name}}</view>
      <view class="favi">
            <uni-icons type="star" size="18" color="gray"></uni-icons>
            <text>收藏</text>
          </view>
            <view class="yf">快递：免运费</view>
            
        </view>
            <rich-text :nodes="goods_info.goods_introduce"></rich-text>   
            
            <!-- 商品导航组件 -->
            <view class="goods_nav">
              <!-- fill 控制右侧按钮的样式 -->
              <!-- options 左侧按钮的配置项 -->
              <!-- buttonGroup 右侧按钮的配置项 -->
              <!-- click 左侧按钮的点击事件处理函数 -->
              <!-- buttonClick 右侧按钮的点击事件处理函数 -->
              <uni-goods-nav :fill="true" :options="options" :buttonGroup="buttonGroup" @click="onClick" @buttonClick="buttonClick" />
            </view>

            
            
    </view> 
       
</template>
  
<script>
  export default {
    data() {
      return {
       // 商品详情对象
           goods_info: {},
           // 左侧按钮组的配置对象
           options: [{
             icon: 'shop',
             text: '店铺'
           }, {
             icon: 'cart',
             text: '购物车',
             info: 2
           }],
           // 右侧按钮组的配置对象
           buttonGroup: [{
               text: '加入购物车',
               backgroundColor: '#ff0000',
               color: '#fff'
             },
             {
               text: '立即购买',
               backgroundColor: '#ffa200',
               color: '#fff'
             }
           ]
         }
      ;
    },
    onLoad(options) {
      // 获取商品 Id
      const goods_id = options.goods_id
      // 调用请求商品详情数据的方法
      this.getGoodsDetail(goods_id)
    },
    methods: {
      // 定义请求商品详情数据的方法
      async getGoodsDetail(goods_id) {
        const { data: res } = await uni.$http.get('/api/public/v1/goods/detail', { goods_id })
        if (res.meta.status !== 200) return uni.$showMsg()
        res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block;" ').replace(/webp/g, 'jpg')
        // 为 data 中的数据赋值
        this.goods_info = res.message
      },
      preview(i){
        uni.previewImage({
            // 预览时，默认显示图片的索引
            current: i,
            // 所有图片 url 地址的数组
            urls: this.goods_info.pics.map(x => x.pics_big)
          })
      }  ,
      onClick(e) {
        if (e.content.text === '购物车') {
          // 切换到购物车页面
          uni.switchTab({
            url: '/pages/cart/cart'
          })
        }
      } 
    } 

  }
</script>

<style lang="scss">
.goods-info-box {
  padding: 10px;
  padding-right: 0;

  .price {
    color: #c00000;
    font-size: 18px;
    margin: 10px 0;
  }

  .goods-info-body {
    display: flex;
    justify-content: space-between;

    .goods-name {
      font-size: 13px;
      padding-right: 10px;
    }
    // 收藏区域
    .favi {
      width: 120px;
      font-size: 12px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      border-left: 1px solid #efefef;
      color: gray;
    }
  }

  // 运费
  .yf {
    margin: 10px 0;
    font-size: 12px;
    color: gray;
  }
}
.goods-detail-container {
  // 给页面外层的容器，添加 50px 的内padding，
  // 防止页面内容被底部的商品导航组件遮盖
  padding-bottom: 50px;
}

.goods_nav {
  // 为商品导航组件添加固定定位
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
}

</style>
 