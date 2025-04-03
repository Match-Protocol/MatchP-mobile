<template>
  <u-popup v-model="isShortTerm" mode="bottom" :border-radius="16" :height="`${bottomHeight + setHeight}rpx`">
    <view class="tl-popup">
      <view class="tl-flex-row-bwt">
        <view class="tl-font-no" @tap="isShortTerm = false">取消</view>
        <view class="title">{{title}}</view>
        <view class="tl-font-yes" @tap="saveMsg">确定</view>
      </view>
      <u-input v-model="introduce" :type="inputType" :placeholder-style="placeholderStyle" :clearable="false" :height="setInputHeight"
               :border="true" :placeholder="placeholder" @input="checkDisabled" @focus="onModuleFocus" @blur="onModuleBlur"/>
<!--      :maxlength="maxlength"-->
      <text class="tl-tips">{{introduce.length}}/{{maxlength}}</text>
    </view>
  </u-popup>
</template>

<script>
export default {
  data() {
    return {
      introduce:'',
      curKyeHeight:0,
      bottomHeight:0,
      fixedBottomHeight:0,
    };
  },
  props: {
    title:{
      type: String,
      default: ""
    },
    placeholder:{
      type: String,
      default: ""
    },
    maxlength:{
      type: Number,
      default: 30
    },
    curIntroduce:{
      type: String,
      default: ""
    },
    placeholderStyle: {
      type: String,
      default: "color: #B8B8B8"
    },
    shortTerm: {
      type: Boolean,
      default: false
    },
    setHeight: {
      type: Number,
      default: 500
    },
    inputType: {
      type: String,
      default: "textarea"
    },
    setInputHeight: {
      type: Number,
      default: 236
    }
  },
  watch:{
    curIntroduce: {
      handler(newVal) {
        this.$log("curIntroduce", newVal)
        this.introduce = newVal?newVal:'';
      },
      immediate: true
    },
    introduce:{
      handler(newVal, oldVal) {
        if (newVal.length > this.maxlength) {
          setTimeout(() => {
            this.setNum();
          }, 0);
        }
      }
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
  mounted() {
    this.$log("input-pop加载offKey")
    uni.onKeyboardHeightChange(this.listenerKey);
  },
  destroyed() {
    this.$log("input-pop卸载offKey")
    uni.offKeyboardHeightChange(this.listenerKey)
  },
  methods:{
    onModuleFocus(event) {
      // this.$log('onFocus',event);
      this.bottomHeight = this.fixedBottomHeight;
    },
    onModuleBlur(event) {
      // this.$log('onBlur',event);
      this.bottomHeight = 0;
    },
    checkDisabled(e) {
      this.$emit("checkDisabled",e)
    },
    setNum() {
      this.introduce = this.introduce.slice(0, this.maxlength);
    },
    listenerKey(res) {
      this.$log("listenerKey", res.height)
      if (res.height === 0) {
        // 隐藏键盘
        this.bottomHeight = 0;
        // this.$log("隐藏键盘", res.height)

      } else {
        // 显示键盘
        this.$log("显示键盘", res.height)
        this.curKyeHeight = res.height;
        // #ifdef MP-WEIXIN
        this.setKeyHeight();
        // #endif
      }
    },
    // 获取真实键盘高度
    setKeyHeight() {
      let self = this;
      uni.getSystemInfo({
        success: (res) => {
          self.$log("sysinfo", res.screenHeight, res.windowHeight)
          // // 虚拟键位高度
          // let heightDiff = res.screenHeight - res.windowHeight
          // // 获取键盘高度减去虚拟键位高度
          // let diff = this.curKyeHeight - heightDiff
          // this.keyboardHeight = diff > 0?diff:0
          // self.$log("iso高度设定1",this.keyboardHeight,(this.keyboardHeight * (750 / res.windowWidth)))
          // this.keyboardHeight = (this.keyboardHeight * (750 / res.windowWidth)) + 64 //将px 转换rpx

          self.bottomHeight = self.fixedBottomHeight = (self.curKyeHeight * (750 / res.windowWidth));
          self.$log("设置高度",self.bottomHeight)
        }
      })
    },
    saveMsg() {
      this.isShortTerm = false;
      this.$emit("saveMsg", this.introduce);
      this.introduce = '';
    },
  }
};
</script>

<style lang="scss" scoped>
//弹窗相关
//::v-deep .u-drawer-content {
//  // overflow: visible !important;
//  background-color: rgba(98, 98, 98, 0.3) !important;
//}
::v-deep .u-input__input {
  background: #F7F7F7;
  border-radius: 12rpx;
  padding: 0 30rpx !important;
}
::v-deep .u-input__textarea {
  background: #F7F7F7;
  border-radius: 12rpx;
  padding: 26rpx 30rpx !important;
}
.tl-popup {
  position: relative;
  padding: 0 32rpx;
  //height: 470rpx;
  height: auto;
  .title {
    padding: 24rpx;
    text-align: center;
    font-size: 32rpx;
    font-family: Source Han Sans SC-Medium, Source Han Sans SC;
    font-weight: 500;
    color: #333333;
  }
}
.tl-font-no{
  font-size: 24rpx;
  font-family: Source Han Sans SC-Normal, Source Han Sans SC;
  font-weight: 400;
  color: #666666;
}

.tl-font-yes{
  font-size: 24rpx;
  font-family: Source Han Sans SC-Normal, Source Han Sans SC;
  font-weight: 400;
  color: #1ACDE8;
}
.tl-tips {
  position:absolute;
  bottom: 10rpx;
  right: 50rpx;
}
</style>