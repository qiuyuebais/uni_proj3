<template>
  <view>
    <view class="goods-item">
      <view class="goods-item-left">
        <radio v-if="showRadio" :checked="goods.goods_state" color="#C00000" @click="radioClickHandler"></radio>
        <image :src="goods.goods_small_logo||defaultPic" class="goods-pic" ></image> 
      </view>
      <view class="goods-item-right ">  
        <view class="goods-name">
          {{goods.goods_name}}   
        </view>
        <view class="goods-info-box">
          <view class="goods-price">
            ￥{{goods.goods_price|tofixed}} 
          </view>
          <!-- 商品数量 -->
            <uni-number-box :min="1" :value="goods.goods_count" v-if="showRadio" @change="numChangeHandler"></uni-number-box> 
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    props:{
      goods:{
        type:Object,
        default:{} 
      },
       // 是否展示图片左侧的 radio
          showRadio: {
            type: Boolean,
            // 如果外界没有指定 show-radio 属性的值，则默认不展示 radio 组件
            default: false,
          },
    },  
    name:"my-goods",
    data() {
      return {
           defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
      };
    },
    filters: {
      // 把数字处理为带两位小数点的数字
      tofixed(num) {
        return Number(num).toFixed(2)
      }
    },
     methods: {
       // radio 组件的点击事件处理函数
       radioClickHandler() {
         // 通过 this.$emit() 触发外界通过 @ 绑定的 radio-change 事件，
         // 同时把商品的 Id 和 勾选状态 作为参数传递给 radio-change 事件处理函数
         this.$emit('radio-change', {
           // 商品的 Id
           goods_id: this.goods.goods_id,
           // 商品最新的勾选状态
           goods_state: !this.goods.goods_state
         })
       },
       numChangeHandler(val) {
           // 通过 this.$emit() 触发外界通过 @ 绑定的 num-change 事件
           this.$emit('num-change', {
             // 商品的 Id
             goods_id: this.goods.goods_id,
             // 商品的最新数量
             goods_count: +val
           })
         }
     }
  }
</script>

<style lang="scss">
.goods-item {
  display: flex;
  padding: 10px 5px;
  border-bottom: 1px solid #f0f0f0;

  .goods-item-left {
    margin-right: 5px;
    display: flex;
justify-content: space-between;
align-items: center;
    .goods-pic {
      width: 100px;
      height: 100px;
      display: block;
    }
  }

  .goods-item-right {
    display: flex;
    flex: 1;
    flex-direction: column;
    justify-content: space-between;

    .goods-name {
      font-size: 13px;
    }

    .goods-price {
      font-size: 16px;
      color: #c00000;
    }
  }
}
 .goods-info-box{
   display: flex;
align-items: center;
justify-content: space-between;
 }
</style>
