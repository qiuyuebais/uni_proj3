<template>
  <view>
   <view class="search-box">
     <!-- 基本用法 -->
     <uni-search-bar  @input="input" :radius="100" cancelButton="none"></uni-search-bar>
   </view>
   <view class="sugg-list" v-if="searchResults.length !== 0">
     <view class="sugg-item" v-for="(item,i) in searchResults " :key="i" @click="gotoDetail(item.goods_id)">
        <view class="goods-name">{{item.goods_name}}</view>
           <uni-icons type="arrowright" size="16"></uni-icons>
     </view>
   </view>
   <view v-else class="history-box">
      <view class="history-box">
        <view class="history-title">
          <text>搜索历史</text>
          <uni-icons @click="clearList" type="trash" size="17"></uni-icons>
        </view>
        <view class="history-list"> 
          <uni-tag @click="gotoGoodsList(item)" :text="item" v-for="(item,i) in  histories" :key="i"></uni-tag>
        </view>
      </view>
   </view>
  </view>
</template> 

<script>
  export default {
    data() {
      return {
        timer:null,
        kw:null,
        searchResults: [],
        historyList: [],
      };
    },
    onLoad() {
      this.historyList=JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    methods:{ 
      input(e){
        clearTimeout(this.timer)
        this.timer=setTimeout(()=>{
          this.kw=e.value 
          this.getSearchList()
        },500) 
      },
  async getSearchList() {
    // 判断关键词是否为空
    if (this.kw === '') {
      this.searchResults = []
      return
    }
    // 发起请求，获取搜索建议列表
    const { data: res } = await uni.$http.get('/api/public/v1/goods/qsearch', { query: this.kw })
    if (res.meta.status !== 200) return uni.$showMsg()
    this.searchResults = res.message
    this.saveSearchHistory()
  },
  gotoDetail(goods_id){
     uni.navigateTo({
        // 指定详情页面的 URL 地址，并传递 goods_id 参数
        url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
      })
  },
  clearList(){
     this.historyList=[] 
     uni.setStorageSync('kw', '')  
  },
  saveSearchHistory(){
    // 1. 将 Array 数组转化为 Set 对象
      const set = new Set(this.historyList)
      // 2. 调用 Set 对象的 delete 方法，移除对应的元素
      set.delete(this.kw)
      // 3. 调用 Set 对象的 add 方法，向 Set 中添加元素
      set.add(this.kw)
      // 4. 将 Set 对象转化为 Array 数组
      this.historyList = Array.from(set)
      uni.setStorageSync('kw', JSON.stringify(this.historyList)) 
  },
  gotoGoodsList(item){
     uni.navigateTo({
        // 指定详情页面的 URL 地址，并传递 goods_id 参数
        url: '/subpkg/goods_detail/goods_detail?goods_id=' + item
      })
  }
    },
    computed:{
      histories(){
        return [...this.historyList].reverse()
      }
    }
  }
</script>

<style lang="scss">
.search-box{
  position: sticky;
  top: 0;
  z-index: 999;
}
.sugg-list {
  padding: 0 5px;

  .sugg-item {
    font-size: 12px;
    padding: 13px 0;
    border-bottom: 1px solid #efefef;
    display: flex;
    align-items: center;
    justify-content: space-between;

    .goods-name {
      // 文字不允许换行（单行文本）
      white-space: nowrap;
      // 溢出部分隐藏
      overflow: hidden;
      // 文本溢出后，使用 ... 代替
      text-overflow: ellipsis;
      margin-right: 3px;
    }
  }
}
.history-title{
  display: flex; 
  padding: 0 10px;
 justify-content: space-between;   
}
.history-list{
   display: flex; 
   padding-left: 5px;
   flex-wrap: wrap; 
   uni-tag{
      margin-top: 5px;
     margin-right: 5px;
     font-weight: 700;
   }
}
</style>
