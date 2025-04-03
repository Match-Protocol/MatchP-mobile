<template>
  <u-popup v-model="isShortTerm" mode="bottom" :height="setHeight" border-radius="12" :mask-close-able="false"
           :closeable="!isShowOperation">
    <view class="popup-box">
      <view class="popup-title" :class="isShowOperation?'':'popup-title-cancel'">
        <text v-if="isShowOperation" class="tl-close-color" @tap="onClose">取消</text>
        <view class="tl-title">{{curText}}</view>
        <text v-if="isShowOperation" class="tl-text" @tap="onNotarize">确认</text>
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
    isShowOperation: {//是否显示取消确定按钮
      type: Boolean,
      default: true
    },
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
      default: '提示'
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
    onClose() {
      this.$emit("onClose")
    }
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
  //padding-top: 36rpx;
  padding-bottom: 68rpx;
  .popup-title {
    width: 100%;
    height: 122rpx;
    padding: 0 48rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 500;
    font-size: 32rpx;
    color: #000000;
    line-height: 32rpx;
    text-align: left;
  }
  .popup-title-cancel {
    justify-content: center !important;
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