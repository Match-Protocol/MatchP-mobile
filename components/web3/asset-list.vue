<template>
  <view class="content-box">
    <block v-if="dataList && dataList.length > 0">
      <view class="content-box-item" v-for="(item,index) in dataList" :key="index" @tap="ondetail(index)">
<!--        <view class="tl-icon" :class="item.digital_type === 1?'tl-icon-none':''">-->
        <view class="tl-icon">
          <block v-if="item.digital_type === 2">
<!--            <image :lazy-load="true" class="tl-img-94 tl-img-hexagonal" :src="item.picture"/>-->
            <u-image class="tl-img-94 tl-img-hexagonal" :src="item.picture" :fade="false"
                     :height="155" duration="100" :lazy-load="true" bg-color="#454545">
              <u-loading slot="loading"></u-loading>
            </u-image>
            <image :lazy-load="true" class="tl-img-94-border" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/web3/medal_border.png"/>
          </block>
          <block v-else-if="item.digital_type === 1">
<!--            <image :lazy-load="true" class="tl-img-94-none" :src="item.picture"/>-->
            <u-image class="tl-img-94-none" :src="item.picture" :fade="false" border-radius="12"
                     :height="140" duration="100" :lazy-load="true" bg-color="#454545">
              <u-loading slot="loading"></u-loading>
            </u-image>
            <image :lazy-load="true" class="tl-img-94-none-border" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/web3/medal_border1.png"/>
          </block>
          <view class="tl-icon-tips" style="z-index: 1" v-if="seriesType === 1">{{item.digital_num}}</view>
<!--          <view style="z-index: 99">{{item.digital_num}}</view>-->
        </view>
        <text class="tl-font-28-00 tl-text-overflow-1">{{seriesType === 1?item.series_name:item.digital_name}}</text>
      </view>
    </block>
    <block v-else>
      <view class="tl-section-2">
        <view class="tl-img-nodata tl-center">
          <text v-if="hintType === 0" class="tl-font-nodata">暂无{{classifyList[type]}}</text>
        </view>
      </view>
      <view  v-if="hintType === 1" class="tl-tips-box">
        <text class="tl-font-32-00">还不能铸造</text>
        <text>创建社区成为主理人</text>
        <text>并拥有web3账号才能去铸造哦</text>
<!--        <view>创建社区</view>-->
      </view>
    </block>
  </view>
</template>
<script>

export default {
  data() {
    return {
      classifyList:['收集','铸造','藏品','勋章','资产'],
    }
  },
  props:{
    dataList:{
      type:Array,
      default:null
    },
    noMoreData:{
      type:Boolean,
      default:false
    },
    type:{//1收集2铸造
      type:Number,
      default:0
    },
    seriesType:{//0个人1系列
      type:Number,
      default:0
    },
    hintType:{//0暂无xx 1提示创建社区
      type:Number,
      default:0
    }
  },
  mounted() {
    console.log("当前的数据",this.hintType)
  },
  methods:{
    ondetail(index) {
      this.$emit("onDetail",this.dataList,index)
    }
  }
}
</script>

<style scoped lang="scss">
.content-box {
  width: 100%;
  height: auto;
  display: flex;
  flex-wrap: wrap;
  .content-box-item {
    width: 200rpx;
    height: 240rpx;
    padding: 0 30rpx;
    //margin: 2rpx 7rpx;
    margin-right: 14rpx;
    display: flex;
    flex-direction: column;
    align-items: center;
    //background: #8a6de9;

    flex: 1;
    width: calc((100% - 28rpx) / 3);  // 这里的10px = (分布个数3-1)*间隙14rpx, 可以根据实际的分布个数和间隙区调整
    min-width: calc((100% - 28rpx) / 3); // 加入这两个后每个item的宽度就生效了
    max-width: calc((100% - 28rpx) / 3); // 加入这两个后每个item的宽度就生效了
    &:nth-child(3n) { // 去除第3n个的margin-right
      margin-right: 0;
    }

  }
}
.tl-img-box {
  position: absolute;
}
.tl-img-94 {
  position: absolute;
  width: 155rpx;
  height: 155rpx;
  min-width: 155rpx;
  //background-color: #e777d2; /* 边框颜色 */
  ////clip-path: polygon(50% 0%, 100% 25%, 100% 75%, 50% 100%, 0% 75%, 0% 25%);
  //clip-path: polygon(52% 3%, 88% 24%, 90% 72%, 50% 94%, 10% 71%, 10% 25%)
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
.tl-img-94-border {
  position: absolute;
  width: 180rpx;
  height: 180rpx;
  min-width: 180rpx;
}
.tl-img-94-none {
  position: absolute;
  width: 140rpx;
  height: 140rpx;
  min-width: 140rpx;
  border-radius: 12rpx;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
.tl-img-94-none-border {
  position: absolute;
  width: 170rpx;
  height: 170rpx;
  min-width: 170rpx;

  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
.tl-font-28-00 {
  font-family: PingFang SC, PingFang SC;
  font-weight: 400;
  font-size: 26rpx;
  color: #000000;
  line-height: 40rpx;
  text-align: center;
}
.tl-icon {
  position: relative;
  width: 180rpx;
  height: 180rpx;
  margin-bottom: 22rpx;
  display: flex;
  justify-content: center;
  align-items: center;
  .tl-icon-tips {
    position: absolute;
    bottom: 25rpx;
    left: 50%;
    transform: translateX(-50%);
    width: 68rpx;
    height: 32rpx;
    background: rgba(0,0,0,0.3);
    border-radius: 20rpx 20rpx 20rpx 20rpx;
    font-family: PingFang SC, PingFang SC;
    font-weight: 600;
    font-size: 24rpx;
    color: #F6F6F6;
    line-height: 32rpx;
    text-align: center;
    z-index: 5;
  }
}
.tl-icon-none {
  width: 100rpx;
  height: 100rpx;
  margin-bottom: 22rpx;
  margin-top: 10rpx;
  >view {
    z-index: 5;
    bottom: -12rpx !important;
  }
}
.tl-tips-box {
  position: relative;
  top: -230rpx;
  width: 100%;
  height: auto;
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 400;
  font-size: 28rpx;
  color: #767676;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  >view {
    width: 240rpx;
    height: 80rpx;
    margin-top: 26rpx;
    background: linear-gradient( 90deg, #21FFAB 0%, #A8FF6D 100%);
    border-radius: 44rpx;
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 500;
    font-size: 32rpx;
    color: #454545;
    line-height: 80rpx;
    text-align: center;
  }
}
.tl-font-32-00 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: bold;
  font-size: 32rpx;
  color: #000000;
  line-height: 44rpx;
  text-align: center;
}
</style>