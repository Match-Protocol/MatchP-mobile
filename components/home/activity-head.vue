<template>
  <view class="join-head">
    <block v-if="activityData.length > 0" v-for="(item,index) in activityData" :key="index">
<!--      <image v-if="index < 5" class="tl-img-80" :src="item" :lazy-load="true"></image>-->
      <u-lazy-load v-if="index < 5" class="tl-img-80" :image="item" :loading-img="headImg"
                   height="48" border-radius="24" :error-img="headImg" img-mode="heightFix"></u-lazy-load>
<!--      <u-image v-if="index < 5" class="tl-img-80" :src="item" :loading-icon="headImg" :error-icon="headImg" shape="circle"-->
<!--               height="48" width="48" duration="300" :lazy-load="false" border-radius="24" :fade="true">-->
<!--&lt;!&ndash;        <u-loading slot="loading" size="22"></u-loading>&ndash;&gt;-->
<!--      </u-image>-->
    </block>
  </view>
</template>

<script>
export default {
  data() {
    return {
      headImg:'https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/head.jpg',
    };
  },
  props:{
    activityData:{
      type:Array,
      default:[]
    }
  },
  onLoad() {
  },
  onShow() {
  }
}
</script>

<style lang="scss" scoped>
.join-head {
  //width: calc(34rpx * 5);
  position: relative;
  display: flex;
  align-items: center;
  .tl-img-80 {
    position: relative;
    width: 48rpx;
    height: 48rpx;
    border-radius: 50%;
    border: 2rpx solid #FFFFFF;
  }
  .tl-img-80:nth-child(1) {
    left: 0rpx;
  }
  .tl-img-80:nth-child(2) {
    left: -12rpx;
  }
  .tl-img-80:nth-child(3) {
    left: -24rpx;
  }
  .tl-img-80:nth-child(4) {
    left: -36rpx;
  }
  .tl-img-80:nth-child(5) {
    left: -48rpx;
  }
  //>image {
  //	position: relative;
  //	width: 34rpx;
  //	height: 34rpx;
  //	border-radius: 50%;
  //}
  //image:nth-child(1) {
  //	left: 0rpx;
  //}
  //image:nth-child(2) {
  //	left: -12rpx;
  //}
  //image:nth-child(3) {
  //	left: -24rpx;
  //}
  //image:nth-child(4) {
  //	left: -36rpx;
  //}
  //image:nth-child(5) {
  //  left: -48rpx;
  //}
}
</style>