<template>
  <view class="tl-content" @tap.stop="goTribe(dataItem)">
    <view class="tl-flex-row-align-center mb-40">
      <view class="tl-img-box">
        <image class="tl-img-112" :src=dataItem.tri_avatar />
        <!--          0=未加入1=创建者、2=共创员、3=成员-->
        <view v-if="dataItem.role === 1" class="tl-img-tips">
          <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/tribe/owner_icon.png" />
          <text>主理人</text>
        </view>
        <view v-else-if="dataItem.role === 2" class="tl-img-tips2">共创者</view>
      </view>
      <view class="tl-content-text">
        <view class="tl-content-title tl-flex-row-start mb-30">
          <text class="tl-font-32-00 tl-text-overflow-1" :class="btnType === 2?'tl-max-65':'tl-max-75'">{{dataItem.tribe_name}}</text>
          <view v-if="type==1" class="tl-tips u-bg-c-subject">场地</view>
          <typeTips v-else :name="tipsType[(dataItem.label - 1) || 0]" style="margin-left: 6rpx" :setSize="25"></typeTips>
          <!--            <view v-else class="tl-tips u-bg-c-subject">{{tipsType[dataItem.label - 1]}}</view>-->

          <view v-if="btnType === 2" @tap.stop="goManageInfo(dataItem)" class="tl-btn-set">•••</view>
        </view>
        <view v-if="btnType === 1" class="tl-authentication-box">
          <view class="tl-authentication">
            <image
                src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/community_list.png"/>
            <text class="tl-text-overflow-1">{{!/^ *$/.test(dataItem.administrative_region)?dataItem.administrative_region:'社区未认证'}}</text>
          </view>
        </view>
        <text v-else-if="btnType === 2" class="tl-font-24-3d">{{dataItem.member_num}}人已加入</text>
      </view>

      <!--        审核状态0=未申请、1=待审核、2=审核通过、3=审核不通过-->
      <block v-if="btnType === 1">
        <view v-if="dataItem.examine_state === 0" @tap.stop="goJoin(dataItem)" class="tl-btn-add">
          {{dataItem.examine?'申请加入':'加入'}}
        </view>
        <view v-else-if="dataItem.examine_state === 1" class="tl-btn-add2">已申请</view>
        <view v-else-if="dataItem.examine_state === 2" class="tl-btn-add2">已加入</view>
        <view v-else-if="dataItem.examine_state === 3" class="tl-btn-add2">已忽略</view>
      </block>
    </view>
    <text class="tl-font-28-93 tl-text-overflow-1">{{dataItem.brief || "这个人很懒，什么都没留下"}}</text>
  </view>
</template>

<script>
	import typeTips from "../activitv/type-tips.vue";

  export default {
    components: {typeTips},
    data() {
      return {
        list:[],
      }
    },
		props:{
      dataItem:{
        type:Object,
        default:{}
      },
      type:{
        type:Number,
        default:0
      },
      btnType:{//1.推荐 2.社区设置菜单 3.用户详情社区
        type:Number,
        default:3
      },
		},
    // watch:{
    //   dataList:{
    //     deep:true,
    //     immediate: true,
    //     handler:function(newVal,oldVal) {
    //       this.list = newVal;
    //       this.$log('newVal:',newVal)
    //     }
    //   }
    // },
    mounted() {
      // this.$log("我是组件",this.dataItem)
    },
    methods:{
      goManageInfo(id) {
        this.$emit("goManageInfo",id)
      },
      goTribe(item) {
        this.$emit("goTribe",item)
      },
      goJoin(item) {
        this.$emit("goJoin",item)
      }
    }
	}
</script>

<style lang="scss" scoped>
.tl-content {
  width: 686rpx;
  height: 240rpx;
  margin: 0 auto;
  background: #FFFFFF;
  border-radius: 20rpx;
  padding: 24rpx 32rpx 0 20rpx;
  margin-bottom: 20rpx;
  .tl-content-text {
    flex: 1;
    height: 100%;
    margin-left: 20rpx;
    display: flex;
    flex-direction: column;
    justify-content: center;
    .tl-content-title {


    }
  }
}
.tl-img-box {
  position: relative;
  width: 112rpx;
  height: 112rpx;
  .tl-img-112 {
    position: absolute;
    width: 112rpx;
    height: 112rpx;
    min-width: 112rpx;
    border-radius: 12rpx;
  }
  .tl-img-tips {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 28rpx;
    background: rgba(0,0,0,0.5);
    border-radius: 12rpx;
    display: flex;
    justify-content: center;
    align-items: center;
    >image {
      width: 20rpx;
      height: 20rpx;
      min-width: 20rpx;
      margin-right: 4rpx;
    }
    >text {
      font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
      font-weight: 700;
      font-size: 24rpx;
      color: #50F5FF;
      line-height: 28rpx;
      text-align: center;
    }
  }
  .tl-img-tips2 {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 28rpx;
    background: rgba(0,0,0,0.5);
    border-radius: 12rpx;
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 700;
    font-size: 24rpx;
    color: #FFFFFF;
    line-height: 28rpx;
    text-align: center;
  }
}

.tl-font-28-93 {
  font-family: Source Han Sans, Source Han Sans;
  font-weight: 400;
  font-size: 28rpx;
  color: rgba(147,147,147,0.8);
  line-height: 28rpx;
  text-align: left;
}
.tl-font-32-00 {
  font-size: 32rpx;
  font-family: PingFang SC, PingFang SC;
  font-weight: 400;
  color: #000000;
}
.tl-tips {
  font-size: 24rpx;
  font-family: PingFang SC, PingFang SC;
  font-weight: 400;
  color: #FFFFFF;
  margin-left: 20rpx;
  line-height: 24rpx;
  padding: 4rpx 14rpx;
  border-radius: 8rpx;
}
.tl-btn-set {
  width: 76rpx;
  height: 44rpx;
  background: #FFFFFF;
  border-radius: 40rpx;
  border: 2rpx solid rgba(0,0,0,0.1);
  font-size: 30rpx;
  font-weight: bold;
  color: #454545;
  line-height: 44rpx;
  text-align: center;
  margin-left: auto;
}
.tl-font-24-3d {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 400;
  font-size: 24rpx;
  color: #3D3D3D;
  line-height: 24rpx;
  text-align: left;
}
.tl-btn-add {
  width: 140rpx;
  height: 60rpx;
  background: #50F5FF;
  border-radius: 30rpx;
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 500;
  font-size: 28rpx;
  color: #454545;
  line-height: 60rpx;
  text-align: center;
  animation: flipInY 0.8s ease forwards;
}
.tl-btn-add2 {
  width: 140rpx;
  height: 60rpx;
  //background: #50F5FF;
  border-radius: 30rpx;
  border: 2rpx solid #989898;
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 500;
  font-size: 28rpx;
  color: #454545;
  line-height: 60rpx;
  text-align: center;
  animation: flipInY 0.8s ease forwards;
}
.tl-authentication-box {
  width: 100%;
  height: auto;
  display: flex;
  .tl-authentication {
    width: auto;
    //max-width: 32%;
    //height: 52rpx;
    //padding: 0 16rpx 0 6rpx;
    //background: rgba(60,200,255,0.3);
    //border-radius: 40rpx;
    display: flex;
    align-items: center;
    >image {
      width: 32rpx;
      height: 32rpx;
      min-width: 32rpx;
      margin-right: 4rpx;
    }
    >text {
      font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
      font-weight: bold;
      font-size: 24rpx;
      color: #3D3D3D;
      line-height: 24rpx;
      text-align: left;
    }
  }
}
.tl-max-65 {
  max-width: 65%;
}
.tl-max-75 {
  max-width: 70%;
}
</style>
