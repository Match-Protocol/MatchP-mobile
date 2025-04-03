<template>
  <view :class="curTemp?'isActive':'active'">
    <uni-swiper-dot :info="banners" :current="current" field="content" :mode="mode" :dots-styles="dotsStyles">
      <swiper class="swiper swiper-box" :indicator-dots="indicatorDots" :autoplay="autoplay" @change="change"
              :interval="interval" :duration="duration">
        <swiper-item v-for="(item, index) in banners" :key="index" :id="index" @tap="false && clickBanner()">
          <image class="banner-img" :src="item.url" mode="aspectFill"></image>
        </swiper-item>
      </swiper>
    </uni-swiper-dot>
  </view>
</template>

<script>
export default {
  props: {
    banners: {
      type: Array,
      default: []
    },
    curTemp: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      indicatorDots: false,
      autoplay: true,
      interval: 5000,
      duration: 1000,
      current:0,
      mode:'round',
      dotsStyles:{
        bottom:6,
        backgroundColor:'#6D6C6B',
        selectedBackgroundColor:'#50F5FF',
        selectedBorder:'0'
      }
    };
  },
  created() {
    // this.banners = this.banners.map((item, index) => {
    // 	 item.img_url = this.$common.getImgUrl(item.img)
    // 	 return item
    // })
  },
  methods: {
    // 点击banner
    clickBanner(e) {
      let url = this.banners[e.currentTarget.id].value;
      let tempType = this.banners[e.currentTarget.id].type;
      console.log("clickBanner", url, tempType)
      if (tempType == 1) {
        uni.navigateTo({
          url: '/pages/details/details?id=' + url
        })
      } else {

      }
    },
    change(e) {
      this.current = e.detail.current;
    }
  }
}
</script>

<style scoped lang="scss">
@keyframes fromOpacity {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}

@keyframes toOpacity {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

.isActive {
  animation: fromOpacity .5s linear forwards;
}

.active {
  animation: toOpacity .5s linear forwards;
}

.swiper-box {
  //height: 244rpx;
  //width: 100%;
  height: 268rpx;
  width: 690rpx;
  border-radius: 20rpx;
  margin: auto;
  overflow: hidden;
  ///deep/ .uni-swiper-dots {
  //  position: absolute;
  //  bottom: 50% !important;
  //  left: 10% !important;
  //}
}

//.swiper :deep(.wx-swiper-dot) { // 指示点整个区域
//  position: absolute;
//  bottom: 50% !important;
//  left: 10% !important;
//}
//.swiper :deep(.uni-swiper-dot-active) { // 指示点元素激活（当前选中）状态样式
//  background: #CD9763;
//}
.banner-img {
  /* width: 670rpx; */
  width: 100%;
  //height: 244rpx;
  height: 268rpx;
  /* border-radius: 20rpx; */
}





</style>
