<script>
export default {
  created () {
    
  },
  onShareAppMessage() {//未测试
    return {
      title: '自定义分享标题',
      desc: '自定义分享描述',
      path: '/pages/item?id=' + this.data.id
    }
  }
  
}
</script>

<style>
page {
  display: flex;
  flex-direction: column;
  height: 100%;
   /* margin-top: 178rpx; */
  width: 100%;
  background-color: #f9f9f9;
  font-family: -apple-system-font, 'Helvetica Neue', Helvetica, 'Microsoft YaHei', sans-serif;
}
</style>
