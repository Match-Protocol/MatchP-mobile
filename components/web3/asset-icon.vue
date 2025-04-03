<template>
  <view class="tl-icon" :style="[computedBorderStyle]">
    <!--勋章-->
    <block v-if="type === 2">
<!--      <image :style="[computedStyle]" class="tl-img-property tl-img-hexagonal" :src="iconUrl"/>-->
      <u-image :style="[computedStyle]" class="tl-img-property tl-img-hexagonal" :src="iconUrl" :fade="false"
               :height="computedStyle.height" duration="100" :lazy-load="true" bg-color="#454545">
        <u-loading slot="loading"></u-loading>
      </u-image>
      <image :style="[computedBorderStyle]" class="tl-img-property-border" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/web3/medal_border.png"/>
    </block>
    <!--藏品-->
    <block v-else-if="type === 1">
<!--      <image :style="[computedStyle]" class="tl-img-property-none" :src="iconUrl"/>-->
      <u-image :style="[computedStyle]" class="tl-img-property-none" :src="iconUrl" :fade="false" border-radius="12"
               :height="computedStyle.height" duration="100" :lazy-load="true" bg-color="#454545">
        <u-loading slot="loading"></u-loading>
      </u-image>
      <image :style="[computedBorderStyle]" class="tl-img-property-none-border" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/web3/medal_border1.png"/>
    </block>
  </view>
</template>
<script>
export default {
  data() {
    return {
      size:0.861,
      noneSize:0.823,
    }
  },
  props:{
    iconUrl:{
      type:String,
      default:null
    },
    type:{//1藏品2勋章
      type:Number,
      default:0
    },
    setSize:{
      type:Number,
      default:214
    }
  },
  computed: {
    computedStyle() {
      return {
        width: ((this.type === 1 ? this.noneSize : this.size) * this.setSize).toFixed(1) + "rpx",
        height: ((this.type === 1 ? this.noneSize : this.size) * this.setSize).toFixed(1) + "rpx"
      }
    },
    computedBorderStyle() {
      return {
        width: this.setSize + "rpx",
        height: this.setSize + "rpx"
      }
    },
  },
  mounted() {
    console.log("mounted",this.iconUrl,this.type)
  },
  methods:{
  }
}
</script>

<style scoped lang="scss">
.tl-icon {
  position: relative;
}
.tl-img-property {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
.tl-img-property-border {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
.tl-img-property-none {
  position: absolute;
  border-radius: 12rpx;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
.tl-img-property-none-border {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
</style>