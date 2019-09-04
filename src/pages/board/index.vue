<template>
  <div class="container">
    <!-- <header></header> -->
    <div class="slide">
      <swiper class="swp" indicator-dots="true" autoplay="true" :interval="1000" duration="100">
        
        <swiper-item v-for="item in banner.content" :key="item.id">
          <navigator :url="'../item/main?id='+item.id">
            <img class="img" :src="item.image" mode="aspectFill"/>
          </navigator>
        </swiper-item>

      </swiper>
    </div>
    <div class="board" scroll-y="true">
      
      <navigator :url="'../list/main?key='+item.key+'&title='+item.title" v-for="item of list" :key="item.key">
        <div class="board-item">
          <span class="text">{{item.title}}</span>
          <img class="arrow" src="/static/images/arrowright.png" mode="aspectFill"/>
        </div>
      </navigator>
    </div>
  </div>
</template>

<script>
import douban from '../../utils/douban.js'
export default {
  data () {
    return {
      banner: { 
        key: 'in_theaters',
        title: '正在热映',
        content:[
            // {image:'',id:xx}
          ]
      },
      list: [
        { key: 'coming_soon', title:'即将上映'},
        { key: 'top250', title:'Top250' },
        { key: 'in_theaters', title:'正在热映' },
        // { key: 'us_box', title: '北美票房榜' }  数据格式有误
      ]
    }
  },

  components: {
  },

  methods: {
    
  },

  created () {
    douban({
      url: '/v2/movie/' + this.banner.key,
      data:{count:3}
    }).then(
      res=>{
        if(!res.data.subjects) return;
        let result=[];
        res.data.subjects.map((item)=>{
          result.push({
            image:item.images.large,
            id:item.id
          })
        });
        this.banner.content=result
        // console.log(this.data.banner.content);
      }
    )
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

.slide .swp {
  height: 400rpx;
}

.slide .img {
  height: 400rpx;
  width: 100%;
}

.board {
  display: flex;
  flex: 1;
  flex-direction: column;
}

.board-item {
  display: flex;
  cursor: pointer;
  border: 1rpx solid #eee;
  margin: 40rpx;
  padding: 40rpx;
}

.board-item .text {
  flex: 1;
}

.board-item .arrow {
  height: 40rpx;
  width: 40rpx;
}

</style>
