<template>
  <view>
    
    <view class="goods-list">
      <view v-for="(goods,i) in goodsList" :key="i" @click="gotoDetail(goods)">
   <my-goods :goods="goods"></my-goods>
      </view>
    </view>
    
  </view>
</template>

<script>
  export default {
    data() {
      return {
        queryobj:{
          query:'',
          cid:'',
          pagenum:1, 
          pagesize:10
        },
        goodsList:[],
        total:0,
        isloadig:false
      };
    },
    onLoad(options) {
      this.queryobj.query=options.query?options.query:''
      this.queryobj.cid=options.cid?options.cid:''
      this.getGoodsList()
    } ,
    methods:{
     async getGoodsList(cb) {
         // 发起请求
        this.isloadig=true
        cb&&cb() 
         const { data: res } = await uni.$http.get('/api/public/v1/goods/search', this.queryobj)
         if (res.meta.status !== 200) return uni.$showMsg() 
         // 为数据赋值
         this.goodsList = [...this.goodsList,...res.message.goods]
         this.total = res.message.total
         this.isloadig=false
       },
       gotoDetail(goods){
         uni.navigateTo({
           url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id  
         })
       }
    },
    onReachBottom(){
      if (this.queryobj.pagenum * this.queryobj.pagesize >= this.total) return uni.$showMsg('数据加载完毕！')   
      if(this.isloadig)return;  
      this.queryobj.pagenum++
      this.getGoodsList()
    },
    onPullDownRefresh() {
      // 1. 重置关键数据
      this.queryObj.pagenum = 1
      this.total = 0
      this.isloading = false
      this.goodsList = []
    
      // 2. 重新发起请求
      this.getGoodsList(() => uni.stopPullDownRefresh())
    }
  }
</script>

<style lang="scss">

</style>
