<template>
  <div class="container">
    <!-- <header title="豆瓣搜索"></header> -->
    <div class="header">
      <input class="search"  :placeholder="subtitle" placeholder-class="search-placeholder" focus @change="handleSearch" />
    </div>
    <div scroll-y="true" class="list">
      <div class="tips">
        <div v-if="loading">
          <img class="loading" src="/static/images/loading.gif"/>
          <span>刷新中...</span>
        </div>
      </div>
      <navigator :url="'../item/item?id='+item.id" v-for="item of list" :key="item.id">
        <product :item="item"></product>
      </navigator>
      <div class="tips" v-if="list.length>0">
        <div v-if="hasMore">
          <img  class="loading" src="/static/images/loading.gif" mode="aspectFill"/>
          <span>玩了命的加载中...</span>
        </div>
        <div v-else>
          <span>没有更多内容了</span>
        </div>
      </div>
    </div>
  </div>

</template>

<script>
import douban from '../../utils/douban.js'
export default {
  data () {
    return {
      page: 1,
      size: 20,
      subtitle: '请在此输入搜索内容',
      list: [],
      search: '', 
      loading: false,
      hasMore:false
    }
  },

  components: {
  },

  methods: {
    handleSearch(e) {
      if (!e.target.value) return
      this.list=[];
      this.page=1;
      this.subtitle='加载中...';
      this.hasMore=true
      this.loading=true
      this.search=e.target.value

      this.loadList()
    },
    loadList: function () {
      this.subtitle='加载中...';
      this.hasMore=true
      this.loading=true
      let start = (this.page - 1)*this.size;//计算开始条数
      this.page=this.page+1;
      /* this.setData({
        page:this.page+1
      }); */
      // console.log('https://douban.uieee.com/v2/movie/search?q=' + this.search + '&start=' + start + '&count=' + this.size);
      douban({
        url:'/v2/movie/search',
        data:{
          q:this.search,
          start:start,
          count:this.size
        }
      }).then(
        res=>{
          this.subtitle=res.data.title;
          this.hasMore=false
          this.loading=false
          // this.setData({ loading: false, hasMore: false, subtitle: res.data.title });
          console.log('serach',res.data.subjects)
          if (!res.data.subjects.length) {
            return;
          }

          let result = [];
          res.data.subjects.map((item) => {
            result.push({
              image: item.images.small,
              id: item.id,
              title: item.title,
              average: item.rating.average,
              original_title: item.original_title,
              year: item.year,
              directors: (item.directors.length && item.directors[0].name) || '-'
            })
          });
          this.list=this.list.concat(result)
         /*  this.setData({
            list: this.list.concat(result),
          }); */
          console.log('请求成功', this.list)
          wx.stopPullDownRefresh(); //停止下拉刷新UI
        }
      )
    },
  },

  created () {
    
  },

  onPullDownRefresh: function () {
    this.list=[];
    this.page=1;
    this.loadList();
      // app.wechat.original.stopPullDownRefresh(); //停止下拉刷新UI
  },

  /**
   * 页面上拉触底事件的处理函数
   */
  onReachBottom: function () {
    this.loadList(); //加载更多
  },

}
</script>

<style scoped>
.container {
  display: flex;
  flex: 1;
  flex-direction: column;
  box-sizing: border-box;
}
.search {
    width: 100%;
    padding: 20rpx 40rpx;
    color: #666;
    font-size: 38rpx;
    text-align: center;
  }
  .search-placeholder {
    color: #ccc;
    font-size: 38rpx;
    text-align: center;
  }
  .tips {
    font-size: 28rpx;
    text-align: center;
    padding: 50rpx;
    color: #ccc;

    
  }
  .tips .loading,
  .tips   span {
      vertical-align: middle;
    }

  .tips  .loading {
      width: 40rpx;
      height: 40rpx;
      margin-right: 20rpx;
    }
.header {
  display: flex;
  justify-content: center;
  border-bottom: 1rpx solid #ccc;
}

.header input {
  padding: 20rpx 40rpx;
  color: #999;
  font-size: 38rpx;
  text-align: center;
}

.list {
  display: flex;
  flex: 1;
  flex-direction: column;
}

.list .item {
  display: flex;
  padding: 20rpx 40rpx;
  border-bottom: 1rpx solid #eee;
  cursor: pointer;
}

.list .item .poster {
  width: 128rpx;
  height: 168rpx;
  margin-right: 20rpx;
}

.list .item .meta {
  flex: 1;
}

.list .item .meta .title,
.list .item .meta .sub-title {
  display: block;
  margin-bottom: 15rpx;
}

.list .item .meta .title {
  font-size: 32rpx;
}

.list .item .meta .sub-title {
  font-size: 22rpx;
  color: #c0c0c0;
}

.list .item .meta .artists {
  font-size: 24rpx;
  color: #999;
}

.list .item .rating span {
  display: inline-block;
  width: 40rpx;
  font-size: 28rpx;
  font-weight: bold;
  text-align: center;
  background-color: rgba(247, 76, 49, 0.8);
  color: #fff;
  padding: 10rpx;
  border-radius: 20rpx;
}

/* .list .tips {
  font-size: 28rpx;
  text-align: center;
  padding: 50rpx;
  color: #ccc;
}
 */


</style>
