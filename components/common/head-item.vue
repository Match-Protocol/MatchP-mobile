<template>
  <view class="tl-owner-head" :style="[sizeStyle,borderStyle]">
    <u-image class="tl-icon-92-r50" :src="headImg"
             :loading-icon="defaultImg" :height="imgSize" duration="450" shape="circle" :lazy-load="true"
             :border-radius="setRadius" :width="imgSize" :error-icon="defaultImg" :fade="true">
      <!--            <u-loading slot="loading"></u-loading>-->
    </u-image>
    <image :src="sexImgs[sexType]" :style="{width:sexSize + 'rpx',height:sexSize + 'rpx',bottom:sexBottom}"
           class="tl-img-28"/>
  </view>
</template>

<script>
export default {
  props: {
    sexType: {//男1 女2
      type: Number,
      default: 0
    },
    sexSize: {
      type: Number,
      default: 32
    },
    sexBottom: {
      type: String,
      default: '-8rpx'
    },
    headImg: {
      type: String,
      default: ''
    },
    setSize:{
      type: Number,
      default: 100
    },
    setBorderSize:{
      type: Number,
      default: 6
    },
    setBorderColor:{
      type: String,
      default: '#1ACDE8'
    }
  },
  data() {
    return {
      defaultImg: 'https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/head.jpg',
      sexImgs:[
          "",
        "https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/boy.png",
        "https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/girl.png"
      ],
    };
  },
  created() {
  },
  computed: {
    imgSize() {
      return this.setSize - 8;
    },
    sizeStyle() {
      return {
        'width': this.setSize + "rpx",
        'height': this.setSize + "rpx"
      }
    },
    setRadius() {
      return (this.setSize - 8) / 2;
    },
    borderStyle() {
      return {
        'border': this.setBorderSize + "rpx" + " " +  "solid" + " " + this.setBorderColor
      }
    },
  },
  methods: {

  }
}
</script>

<style scoped lang="scss">
.tl-owner-head {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  //width: 100rpx;
  //height: 100rpx;
  border-radius: 50%;
  //border: 6rpx solid #1ACDE8;
}
.tl-img-28 {
  position: absolute;
  right: 0;
  //bottom: -8rpx;
  //width: 32rpx;
  //height: 32rpx;
}
</style>
