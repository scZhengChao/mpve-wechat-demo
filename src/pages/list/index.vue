<template>
  <div class="container">
    <div class="list">
      <navigator :url="'../item/main?id='+item.id" v-for="item of list" :key="item.id">
        <product :item="item"></product>
      </navigator>
    </div>
  </div>

</template>

<script>
import product from '@/components/product.vue';
import douban from '../../utils/douban.js'
export default {
  data () {
    return {
      title:'数据加载中',//页面第一次渲染用到的值
      type:'',
      page: 1, //页码
      size: 10,//页数
      list: [
        // {id:'', image: '', title: '', average: 4.3, original_title: '', year: '', directors:''}
      ]
    }
  },

  components: {
    product
  },

  methods: {
    loadList(){
      console.log('loadlist..')
      douban({
        url: '/v2/movie/' + this.type,
        data:{
          count: this.size,
          start: this.page++
        },
        loading:true
      }).then(
        res=>{
          console.log('res.......',res.data)
          if(!res.data.subjects) return;
          let result = [];
          res.data.subjects.map((item) => {
            result.push({
              image: item.images.small,
              id: item.id,
              title:item.title,
              average: item.rating.average,
              original_title: item.original_title,
              year:item.year,
              directors: item.directors[0].name
            })
          });
          this.list=result;
          this.title=res.data.title;
          console.log(this.title,this.list[0])
          /* this.setData({
            list:this.list.concat(result),
            title:res.data.title//渲染list时需要在渲染一次title
          }); */

          wx.setNavigationBarTitle({
            title: this.title+' « 电影 « 豆瓣'
          });
          
        }
      )
    }
  },

  /*
    加载
  */
  /**
   * 生命周期函数--监听页面加载
   */
  mounted (options) {

    console.log('mounted');

    this.title = this.$root.$mp.query.title || this.title

    // 类型： in_theaters  coming_soon  us_box
    this.type = this.$root.$mp.query.key || this.type

    console.log(this.type,this.title);
    this.loadList();

  },
  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady () {
    console.log('onready',this.title)
    wx.setNavigationBarTitle({
      title: this.title+' « 电影 « 豆瓣'
    });
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

.header {
  padding-top: 130rpx;
  background:#35495e;
  display: flex;
  justify-content: center;
  border-bottom: 1rpx solid #ccc;
}

.header text {
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


</style>
