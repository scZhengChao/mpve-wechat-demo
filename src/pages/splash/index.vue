<template>
  <view class="container">
    <swiper class="splash" indicator-dots>
      <swiper-item v-for="(item,index) in movies" :key="item.id">
        <image :src="item.image" class="slide-image" mode="aspectFill"/>
        <button class="start" @tap="handleStart" v-if="index == movies.length - 1">立即体验</button>
      </swiper-item>
    </swiper>
  </view>

</template>

<script>
import douban from '../../utils/douban.js'
export default {
  data () {
    return {
      movies: [
        // {image:'',id:xx}
      ]
    }
  },

  components: {
  },

  methods: {
    handleStart(){
      wx.switchTab({
        url: '/pages/board/main',
      })
    }
  },

  onLoad: function (options) {
    const value = wx.getStorageSync('douban_splash')
    if(value){
      wx.switchTab({
        url: '/pages/board/main',
      });
      return;
    }
    douban({
      url: '/v2/movie/coming_soon',
      data: { count: 3 }
    }).then(
      res => {
        if (!res.data.subjects) return;
        let result = [];
        res.data.subjects.map((item) => {
          result.push({
            image: item.images.large,
            id: item.id
          })
        });
        
        this.movies=result;
        console.log(this.movies);
        wx.setStorageSync('douban_splash', true)
      }
    )
  },
}
</script>

<style scoped>
.container {
  height: 100%;
  overflow: hidden;
}

.splash {
  height: 100%;
}
/* 
.splash swiper-item {
  flex: 1;
} */

.splash .slide-image {
  position: absolute;
  height: 100%;
  width: 100%;
  opacity: 0.9;
}

.start {
  position: absolute;
  bottom: 200rpx;
  left: 50%;
  width: 300rpx;
  margin-left: -150rpx;
  background-color: rgba(53, 73, 94, 0.4);
  color: #fff;
  border: 5rpx solid #35495e;
  font-size: 40rpx;
}

</style>
