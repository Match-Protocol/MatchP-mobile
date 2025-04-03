<template>
  <u-popup v-model="isShortTerm" mode="bottom" border-radius="22" :mask-close-able="false"
           :closeable="true" :length="setHeight">
    <view class="popup-box">
      <view class="popup-title">
        <view class="tl-title">{{curText}}</view>
      </view>
      <slot></slot>
    </view>
  </u-popup>
</template>

<script>
export default {
  data() {
    return {};
  },
  props: {
    shortTerm: {
      type: Boolean,
      default: false
    },
    setHeight: {
      type: String,
      default: 'auto'
    },
    curText: {
      type: String,
      default: ''
    }
  },
  computed: {
    isShortTerm: {
      get() {
        return this.shortTerm;
      },
      set(value) {
        this.$emit("changePop", value)
      }
    }
  },
  methods: {
    onNotarize() {
      this.$emit("onNotarize")
    },
  }
};
</script>

<style lang="scss" scoped>
//弹窗相关
//::v-deep .u-drawer-content {
//  // overflow: visible !important;
//  background-color: rgba(98, 98, 98, 0.3) !important;
//  //background-color: #8a6de9 !important;
//}
.popup-box {
  width: 100%;
  //background: #F6F6F6;
  //box-shadow: 0rpx 0rpx 14rpx 0rpx #D7D7D7;
  padding-top: 122rpx;
  //padding-bottom: 68rpx;
  .popup-title {
    position: fixed;
    top: 0;
    width: 100%;
    height: 122rpx;
    padding: 0 48rpx;
    display: flex;
    justify-content: center;
    align-items: center;
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 500;
    font-size: 32rpx;
    color: #000000;
    line-height: 32rpx;
    text-align: left;
    background: #FFFFFF;
    z-index: 1;
  }
}
.tl-title {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 600;
  font-size: 32rpx;
  color: #342A25;
  line-height: 32rpx;
  text-align: center;
}
.tl-text {
  color: #2AD1F3;
}
.tl-close-color {
  color: #808080;
}
</style>