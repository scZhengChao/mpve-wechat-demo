<template>
  <div class="container">
    <!-- <header title="{{title}}"></header> -->
    <div v-if="detail">
      <img class="background" :src="detail.images.large"/>
      <div scroll-y="true" class="container">
        <div class="meta">
          <img class="poster" :src="detail.images.large" mode="aspectFit" @tap='preview'/>
          <span class="title">{{detail.title}}({{detail.year}})</span>
          <span class="info">评分：{{detail.rating.average}}</span>
          <span class="info">导演：<block v-for="item of detail.directors" :key="item.id"> {{item.name}} </block></span>
          <span class="info">主演：<block v-for="item of detail.casts" :key="item.id"> {{item.name}} </block></span>
        </div>
        <div class="summary">
          <span class="label">摘要：</span>
          <span class="content">{{detail.summary}}</span>
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
      title: '加载中',
      detail: null
    }
  },

  components: {
  },

  methods: {
    preview(){
      wx.previewImage({
        current: this.detail.images.large, // 当前显示图片的http链接
        urls: [this.detail.images.large] // 需要预览的图片http链接列表
      })
    }
  },
  onLoad: function (options) {
    douban({
      url: '/v2/movie/subject/' + options.id,
      navLoading:true
    }).then(
      res=>{
        if (res.data.msg) {
          console.log('detail ID有误');
          return;
        }
        this.detail=res.data,
        this.title=res.data.title,
        console.log(this.detail);
        /* this.setData({
          detail: res.data,
          title: res.data.title
        }); */
        wx.setNavigationBarTitle({ title: this.title + ' « 电影 « 豆瓣' }); //加载完了修改
      }
    )
  },

  /**
   * 生命周期函数--监听页面初次渲染完成
   */

  onReady() {
    wx.setNavigationBarTitle({ title: this.title + ' « 电影 « 豆瓣' })//先显示加载中
  }

}
</script>

<style scoped>

.container {
  display: flex;
  flex: 1;
  flex-direction: column;
  box-sizing: border-box;
}

.meta {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 50rpx 40rpx;
}

.poster {
  width: 100%;
  height: 800rpx;
  margin: 20rpx;
}

.title {
  font-style: 42rpx;
  color: #222;
}

.info {
  font-size: 24rpx;
  color: #444;
  margin-top: 20rpx;
  width: 80%;
}

.summary {
  width: 80%;
  margin: 30rpx auto;
}

.label {
  display: block;
  font-size: 30rpx;
  margin-bottom: 30rpx;
}

.content {
  color: #555;
  font-size: 24rpx;
  padding: 2em;
}

.background {
  position: fixed;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  height: 100%;
  width: 100%;
  z-index: -1000;
  opacity: .1;
}

</style>
