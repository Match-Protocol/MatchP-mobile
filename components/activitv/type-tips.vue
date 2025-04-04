<template>
  <view class="tl-tribe-type" :style="[computedStyleHeight]">
    <text :style="[computedStyle]">{{name}}</text>
  </view>
</template>
<script>
export default {
  data() {
    return {}
  },
  props:{
    name:{
      type:String,
      default:''
    },
    setSize:{//不低于15
      type:Number,
      default:20
    }
  },
  computed: {
    computedStyle() {
      return {
        'font-size': this.setSize + "rpx",
        'line-height': this.setSize + "rpx"
      }
    },
    computedStyleHeight() {
      return {
        height:(this.setSize + 8) + "rpx",
        borderRadius:(this.setSize - 12) + "rpx"
      }
    }
  },
  mounted() {
  },
  methods:{
  }
}
</script>

<style scoped lang="scss">
.tl-tribe-type {
  width:auto;
  //height: 36rpx;
  background-color: #454545;
  padding: 0 3px;
  border: 5rpx solid #A4C5E9;
  transform: skew(-15deg);
  //border-radius:4rpx;
  display: flex;
  justify-content: center;
  align-items: center;
  >text {
    font-family: graceFont;
    font-weight: 400;
    //font-size: 20rpx;
    text-align: center;
    //line-height: 20rpx;
    //background: linear-gradient(90deg, #00FFBC 0%, #D8FF95 100%);
    //background: linear-gradient(90deg, #F9A9F2 0%, #B9FBFF 100%);
    background: linear-gradient(90deg, #61F1EE 0%, #E29CE4 100%);
    background-clip: text;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }
}
</style>