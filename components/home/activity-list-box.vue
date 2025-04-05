<template>
	<view class="activity-container" :style="{paddingLeft:paddingRow,paddingRight:paddingRow,paddingTop:paddingColumn,
	paddingBottom:paddingColumn,marginBottom:isBottom,width:setWidth}"
        @tap.stop="onGoDetails(activityItem.id)">
<!--    <view class="activity-info" :class="isType==1 || isType==3?'':'tl-marbin-0'">-->
		<view class="activity-info" :class="!isPriceShow?'tl-margin-bottom':''">
      <!--首页海报图和状态-->
			<view class="info-cover" :class="isType==1 || isType==3?'info-left':'info-left-tribe'">
<!--        <u-lazy-load class="tl-img-cover" :image="activityItem.thumb?activityItem.thumb:''" :loading-img="loadingImg"-->
<!--                     border-radius="12" :height="isType==1 || isType==3?'200':'176'" is-effect="false" duration="100"-->
<!--                     :loading-icon="loadingImg" :lazy-load="true"></u-lazy-load>-->
<!--        :loading-icon="loadingImg"-->
<!--        :height="isType==1 || isType==3?'192':'176'" duration="100" :lazy-load="true" border-radius="12"-->


        <u-image v-show="activityItem.thumb" class="tl-img-cover" :src="activityItem.thumb?activityItem.thumb:''" :fade="true"
                 :height="isType==1 || isType==3?'200':'176'" duration="200" :lazy-load="false" border-radius="12"
                 bg-color="#454545">
          <u-loading slot="loading"></u-loading>
        </u-image>
        <view v-if="activityItem.status" class="tl-stats tl-font-24-45"
              :class="{'tl-stats-active':activityItem.status == 4 || activityItem.status == 5,'tl-stats-active2':activityItem.status == 6}">
          {{activityItem.statusName}}
        </view>
        <!--是否是发布人-->
        <view v-if="activityItem.role === 1" class="tl-img-tips">
          <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/tribe/owner_icon.png" />
          <text>发布人</text>
        </view>
        <!--对外隐藏-->
        <image v-if="activityItem.is_hide" class="tl-conceal-img" :class="isType==1 || isType==3?'info-left':'info-left-tribe'"
               src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/act/conceal_icon.png"/>
      </view>
      <!--右侧文本-->
			<view :class="isType==1 || isType==3?'info-right':'info-right-tribe'">
				<view class="tl-flex-row-start">
          <h2 class="title-h2">{{activityItem.name}}</h2>
          <view v-if="isActivityManage == 1 && (activityItem.status != 1 || activityItem.status != 2)"
                @tap.stop="onGoManage(activityItem)" class="tl-btn-set">•••</view>
        </view>

        <view v-if="(isType == 1 || isType == 3 || isType == 2) && activityItem.tri_avatar" class="iconfont-size">
          <image class="tl-img-30" :src="activityItem.tri_avatar"></image>
          <h3 class="tl-font-24-6d tl-width-max-220 tl-text-overflow-1">{{activityItem.tribe_name || ''}}</h3>
<!--          <view class="tl-tips">{{tipsType[activityItem.label - 1]}}</view>-->
          <typeTips :name="tipsType[activityItem.label - 1]" :setSize="20"></typeTips>
        </view>

        <view v-if="isType == 3" class="iconfont-size">
          <image class="image-24" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/acreage_icon.png"></image>
          <span class="info-font-gray-20">面积 {{activityItem.acreage}}m²</span>
        </view>
				<view v-else class="iconfont-size">
					<image class="image-24" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/time.png"></image>
					<span class="info-font-gray-20">{{activityItem.start_time}}-{{activityItem.end_time}}</span>
				</view>

				<view class="iconfont-size address-distance">
          <block v-if="activityItem.address">
            <image class="image-24" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/location.png"></image>
            <block v-if="isShowKm">
              <span class="font-24-66">{{activityItem.distance}}km</span>
              <view class="tl-line"></view>
            </block>
            <span class="info-font-gray-20">{{activityItem.address}}</span>
          </block>
          <block v-else>
            <image class="image-24" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/location-on-line.png"></image>
            <span class="info-font-gray-20">线上活动</span>
          </block>
				</view>
			</view>
		</view>
    <view class="activity-join" v-if="isPriceShow">
      <activityHead :activityData="activityItem.hea_Icons"></activityHead>
			<text v-if="activityItem.apply_users_tota > 0" class="join-text" :style="{left:activityItem.setting + 'rpx'}">{{activityItem.apply_users_tota + (isType == 3?'人已预约':'人已报名')}}</text>
<!--      <text v-else class="join-text" :style="{left:activityItem.setting + 'rpx'}">暂无{{isType == 3?'预约':'报名'}},快冲吧!</text>-->
      <text v-else class="join-text" :style="{left:activityItem.setting + 'rpx'}">快去{{isType == 3?'预约':'报名'}}吧！</text>
      <view class="join-pay" v-if="isActivityManage == 0">
				<view class="tl-button tl-flex-row-center2">
<!--          <block v-if="activityItem.apply_price > 0">-->
<!--            <view class="tl-circle-exclamation"></view>-->
<!--            <text>活动费用：</text>-->
<!--          </block>-->

<!--          <image class="tl-img-35" v-if="activityItem.apply_seed" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/wanzi.jpg"></image>-->
<!--          <text v-if="isType == 3" class="tl-font-24-fe">{{activityItem.apply_price > 0?'￥' + activityItem.apply_price + '起':'免费'}}</text>-->
<!--          <text v-else class="tl-font-24-fe">{{activityItem.apply_price > 0?activityItem.apply_price.toFixed(2) + '元/人':'免费'}}</text>-->

          <view class="tl-button2-box tl-flex-row-center2" :class="activityItem.charge_type === 2?'':'tl-button2-box-hide'">
            <text v-if="activityItem.charge_type === 2">押金可退</text>
            <view class="tl-button2 tl-flex-row-start">
              <image class="tl-img-35" v-if="activityItem.apply_seed" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/wanzi.png"/>
              <button v-if="isType == 3" class="u-b-b-subject-border-change tl-flex-row-center2" :class="activityItem.apply_price > 0?'cur-button':''">
                <text class="tl-font-24">{{activityItem.apply_price > 0?'￥' + activityItem.apply_price + '起':'免费'}}</text>
              </button>
              <button v-else class="u-b-b-subject-border-change tl-flex-row-center2" :class="activityItem.apply_price > 0?'cur-button':''">
                <text class="tl-font-28" v-if="activityItem.apply_price > 0">{{activityItem.apply_price}}</text>
                <text class="tl-font-24" :class="activityItem.apply_price > 0?'tl-font-20':'tl-padding-38'">{{activityItem.apply_price > 0?'元/人':'免费'}}</text>
              </button>
            </view>
          </view>

        </view>
				<!-- <button>￥58.00{{activityItem.id}}</button> -->
			</view>

      <view v-else-if="activityItem.status == 4 || activityItem.status == 5 || activityItem.status == 6 ||
      (activityItem.status == 7 && isActivityManage == 1)"
            class="join-pay" @tap.stop="onGoManage(activityItem)">
        <button class="tl-button u-b-b-subject-hollow tl-flex-row-center2">
          <text class="tl-font-24-fe2">{{isActivityManage == 1?'活动管理':'更多'}}</text>
        </button>
        <!-- <button>￥58.00{{activityItem.id}}</button> -->
      </view>
		</view>
	</view>
</template>

<script>
import activityHead from "../../components/home/activity-head.vue"
import typeTips from "../activitv/type-tips.vue";
	export default {
		data() {
			return {
        headImg:'https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/head.jpg',
        loadingImg:'https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/img_loading.png',
        tipsType:['俱乐部','场地','社群','DAO','社区','小区','学校','村落','协会','单位'],
			};
		},
		props:{
			activityItem:{
				type:Object,
				default:{}
			},
      isPriceShow: {
        type:Boolean,
        default:false,
      },
      paddingRow:{
        type:String,
        default:'20rpx',
      },
      paddingColumn:{
        type:String,
        default:'20rpx',
      },
      isType:{//1.首页和主理人页活动 2.社区活动 3.场地
        type:Number,
        default:1,
      },
      isActivityManage:{
        type:Number,
        default:0,
      },
      isShowKm:{
        type:Boolean,
        default:false,
      },
      isBottom:{
        type:String,
        default:'20rpx',
      },
      setWidth:{//设置宽度
        type:String,
        default:'710rpx',
      },
      isOwner:{//是否是发布者
        type:Boolean,
        default:false,
      }
		},
    components:{
      typeTips,
      activityHead,
    },
		methods:{
			onGoDetails(id) {
				console.log("onGoDetails",id)
				this.$emit("goDetails",id)
			},
      onGoManage(data) {
        console.log("onGoManage",data,this.isType,this.isActivityManage)
        this.$emit("goManage",data);
      },
		}
	}
</script>

<style lang="scss" scoped>
.tl-padding-38 {
  padding: 0 30rpx;
}
.tl-line {
  width: 0;
  height: 24rpx;
  border: 2rpx solid #C4C4C4;
  margin: 0 10rpx;
}
.tl-width-max-220 {
  max-width: 220rpx;
}
.tl-img-30 {
  width: 30rpx;
  height: 30rpx;
  min-width: 30rpx;
  margin-right: 12rpx;
  border-radius: 50%;
}
.tl-img-35 {
  width: 40rpx;
  height: 40rpx;
  min-width: 40rpx;
  margin: 0 6rpx 0 4rpx;
  //vertical-align: center;
}
.tl-tips {
  //background: linear-gradient(270deg, #FF770F 0%, #FFA865 100%);
  background: #42D3AD;
  font-size: 16rpx;
  font-family: PingFang SC, PingFang SC;
  font-weight: 400;
  color: #FFFFFF;
  margin-left: 8rpx;
  //line-height: 16rpx;
  padding: 2rpx 8rpx;
  border-radius: 6rpx;
}
.tl-font-24-6d {
  font-size: 24rpx;
  font-family: PingFang SC, PingFang SC;
  font-weight: 400;
  color: #767676;
  line-height: 24rpx;
  margin-right: 8rpx;
}
.tl-font-24-fe {
  font-family: Alibaba PuHuiTi 3.0, Alibaba PuHuiTi 30;
  font-weight: 500;
  font-size: 24rpx;
  color: #767676;
  line-height: 24rpx;
}
.tl-font-24-fe2 {
  font-family: Alibaba PuHuiTi 3.0, Alibaba PuHuiTi 30;
  font-weight: 500;
  font-size: 24rpx;
  line-height: 24rpx;
}
.tl-marbin-0 {
  margin-bottom: 0 !important;
}
.tl-stats {
  position: absolute;
  width: 100rpx;
  height: 40rpx;
  background: #454545;
  font-size: 24rpx;
  font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
  font-weight: 500;
  color: #FFFFFF;
  line-height: 40rpx;
  text-align: center;
  border-top-left-radius: 12rpx;
  border-bottom-right-radius: 12rpx;
}
.tl-conceal-img {
  position: absolute;
}
.tl-font-24-45 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 500;
  font-size: 24rpx;
  //color: #454545;
  line-height: 40rpx;
  text-align: center;
}
.tl-font-28 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 600;
  font-size: 28rpx;
  line-height: 48rpx;
}
.tl-font-24 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 500;
  font-size: 24rpx;
  line-height: 48rpx;
}
.tl-font-20 {
  font-size: 20rpx !important;
}
.tl-stats-active {
  color: #50F5FF;
  //background: linear-gradient( 90deg, #21FFAB 0%, #A8FF6D 100%);
  background: #454545;
}
.tl-stats-active2 {
  color: #454545;
  //background: linear-gradient( 90deg, #E9FF86 0%, #FFD70F 100%);
  background: linear-gradient( 90deg, #F9A9F2 0%, #B9FBFF 100%);
}
.tl-img-cover {
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 12rpx;
}
.tl-button {
  //width: 138rpx;
  //min-width: 138rpx;
  width:auto;
  height: 44rpx;
  padding: 0 20rpx;
  // background: #000000;
  //box-shadow: 0rpx 6rpx 12rpx 2rpx rgba(0,0,0,0.16);
  //border-radius: 10rpx;
  //border: 3rpx solid #333333;
  font-family: Alibaba PuHuiTi 3.0, Alibaba PuHuiTi 30;
  font-weight: 500;
  font-size: 24rpx;
  //color: #767676;
  text-align: right;
  line-height: 44rpx;
}
.tl-button2-box-hide {
  border:none !important;
  background-image:none !important;
  padding-left:0 !important;
}
.tl-button2-box {
  width:auto;
  height: 48rpx;
  //background: rgba(164,197,233,0.1);
  //border-radius: 42rpx;
  //border: 2rpx solid;
  padding-left: 10rpx;
  //border-image: linear-gradient(80deg, rgba(25.689395517110825, 204.69310104846954, 232.23213851451874, 1), rgba(25.689395517110825, 204.69310104846954, 232.23213851451874, 0)) 2 2;

  /* 设置元素边框为1像素宽度，样式为实线，颜色为透明。 */
  border: 2rpx solid transparent;
  /* 设置元素边框圆角为10像素。 */
  border-radius: 42rpx;
  /* 设置背景剪裁区域为内边距和边框区域。 */
  background-clip: padding-box,border-box;
  /* 设置背景绘制区域为内边距和边框区域。 */
  background-origin: padding-box,border-box;
  /* 设置元素的背景图像为两个线性渐变。第一个渐变从左到右，颜色从白色到白色；第二个渐变以155度角从下左到上右，颜色从rgba(116, 233, 255, 1)到rgba(64, 158, 255, 1)。 */
  background-image:
      linear-gradient(to right, #f5f9fc, #f5f9fc),
      linear-gradient(155deg, rgba(26,205,232, 1), rgba(64, 158, 255, 0));


  >text {
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: bold;
    font-size: 24rpx;
    color: #1ACDE8;
    line-height: 48rpx;
    text-align: left;
  }
  .tl-button2 {
    width:auto;
    height: 100%;
    line-height: 48rpx;
    background: rgba(69,69,69, 0.1);
    border-radius: 42rpx;
    margin-left: 4rpx;
    >button {
      height: 100%;
      border-radius: 42rpx;
      padding: 0 22rpx;
    }
    .cur-button {
      padding: 0 30rpx !important;
    }
  }
}
.tl-circle-exclamation {
  width: 28rpx;
  height: 28rpx;
  min-width: 28rpx;
  border-radius: 50%;
  display: inline-block;
  background-color: #D2D2D2;
  margin-right: 4rpx;
}
.tl-circle-exclamation::before {
  content: "￥";
  font-size: 20rpx;
  color: #fff;
  line-height: 28rpx;
  text-align: center;
  display: block;
}
.activity-container {
	//width: 100%;
	//padding: 20rpx 32rpx;
  background: #FFFFFF;
  //width: 710rpx;
  //margin: 0 auto 30rpx;
  margin-left: auto;
  margin-right: auto;
  border-radius: 24rpx;

	.activity-info {
		width: 100%;
		height: auto;
		margin-bottom: 24rpx;
		display: flex;
    align-items: center;
		// background-color: #72d13a;
    .info-cover {
      position: relative;
      width: 100%;
      height: auto;
      //.info-left {
      //  position: absolute;
      //  width: 296rpx;
      //  height: 192rpx;
      //  //max-height: 244rpx;
      //  background: #FFFFFF;
      //  border-radius: 12rpx;
      //}
      //.info-left-tribe {
      //  position: absolute;
      //  width: 264rpx;
      //  height: 176rpx;
      //  background: #FFFFFF;
      //  border-radius: 12rpx;
      //}
    }
    .info-left {
      width: 284rpx;
      height: 200rpx;
    }
    .info-left-tribe {
      width: 250rpx;
      height: 176rpx;
    }
		.info-right {
      flex: 1;
			//width: 374rpx;
			height: 200rpx;
			margin-left: 16rpx;
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
			// background-color: #FFFFFF;
			.title-h2 {
        flex: 1;
				font-size: 32rpx;
				font-family: Source Han Sans SC-Medium, Source Han Sans SC;
				font-weight: 600;
				color: #1A1A1A;
				//width:348rpx;
				//height: 96rpx;
        //max-height: 96rpx;
				line-height: 48rpx;
				word-break:break-all;
				text-overflow: ellipsis;
				display:-webkit-box;
				-webkit-line-clamp:1;
				-webkit-box-orient:vertical;
				overflow:hidden;
			}
			.iconfont-size {
				// font-size: 21rpx;
				height: 30rpx;
				display: flex;
				align-items: center;
				margin: 8rpx 0;
        overflow: hidden;
				.image-24 {
					width: 28rpx;
					height: 28rpx;
          min-width: 28rpx;
				}
				
			}
			.address-distance {
				height: 30rpx;
				display: flex;
				align-items: center;
				margin: 8rpx 0;
				.image-24 {
					width: 28rpx;
					height: 28rpx;
          min-width: 28rpx;
				}
				.font-24-66 {
					font-size: 24rpx;
					font-family: Source Han Sans SC-Normal, Source Han Sans SC;
					font-weight: 400;
					color: #666666;
					//margin-left: auto
          margin-left: 8rpx;
				}
			}
			
			.info-font-gray-20 {
        //width: 250rpx;
        max-width: 280rpx;
        height: auto;
				margin-left: 8rpx;
				font-size: 24rpx;
				font-family: Source Han Sans SC-Normal, Source Han Sans SC;
				font-weight: 400;
				color: #767676;
				//line-height: 1;
				word-break: break-all;
				text-overflow: ellipsis;
				display: -webkit-box; /** 对象作为伸缩盒子模型显示 **/
				-webkit-box-orient: vertical; /** 设置或检索伸缩盒对象的子元素的排列方式 **/
				-webkit-line-clamp: 1; /** 显示的行数 **/
				overflow: hidden; /** 隐藏超出的内容 **/
			}
		}
    .info-right-tribe {
      //width: 342rpx;
      flex: 1;
      height: 176rpx;
      margin-left: 16rpx;
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      // background-color: #FFFFFF;
      .title-h2 {
        flex: 1;
        font-size: 32rpx;
        font-family: Source Han Sans SC-Medium, Source Han Sans SC;
        font-weight: 600;
        color: #1A1A1A;
        //width:348rpx;
        //height: 96rpx;
        //max-height: 96rpx;
        line-height: 48rpx;
        word-break:break-all;
        text-overflow: ellipsis;
        display:-webkit-box;
        -webkit-line-clamp:1;
        -webkit-box-orient:vertical;
        overflow:hidden;
      }
      .iconfont-size {
        // font-size: 21rpx;
        height: 30rpx;
        display: flex;
        align-items: center;
        margin: 8rpx 0;
        overflow: hidden;
        .image-24 {
          width: 28rpx;
          height: 28rpx;
          min-width: 28rpx;
        }

      }
      .address-distance {
        height: 30rpx;
        display: flex;
        align-items: center;
        margin: 8rpx 0;
        .image-24 {
          width: 28rpx;
          height: 28rpx;
          min-width: 28rpx;
        }
        .font-24-66 {
          font-size: 24rpx;
          font-family: Source Han Sans SC-Normal, Source Han Sans SC;
          font-weight: 400;
          color: #666666;
          //margin-left: auto
          margin-left: 8rpx;
        }
      }

      .info-font-gray-20 {
        //width: 215rpx;
        max-width: 215rpx;
        height: auto;
        margin-left: 8rpx;
        font-size: 24rpx;
        font-family: Source Han Sans SC-Normal, Source Han Sans SC;
        font-weight: 400;
        color: #767676;
        //line-height: 1;
        word-break: break-all;
        text-overflow: ellipsis;
        display: -webkit-box; /** 对象作为伸缩盒子模型显示 **/
        -webkit-box-orient: vertical; /** 设置或检索伸缩盒对象的子元素的排列方式 **/
        -webkit-line-clamp: 1; /** 显示的行数 **/
        overflow: hidden; /** 隐藏超出的内容 **/
      }
    }
	}

	.activity-join {
    position: relative;
		width: 100%;
		height: 48rpx;
		border-radius: 12rpx;
		display: flex;
		align-items: center;
    //padding-left: 16rpx;
		.join-text {
      position: absolute;
      //left: 170rpx;
			font-size: 24rpx;
			font-family: Source Han Sans SC-Normal, Source Han Sans SC;
			font-weight: 400;
			color: #666666;
		}
		.join-pay {
			//width: 128rpx;
      height:100%;
			margin-left: auto;
			display: flex;
			justify-content: flex-end;
			//>image {
			//	width: 52rpx;
			//	height: 52rpx;
			//}
		}
	}
}
.tl-margin-bottom {
  margin-bottom: 0 !important;
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
.tl-img-tips {
  position: absolute;
  bottom: 8rpx;
  right: 8rpx;
  width: 98rpx;
  height: 28rpx;
  background: rgba(0,0,0,0.5);
  border-radius: 14rpx;
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
    font-weight: bold;
    font-size: 20rpx;
    color: #50F5FF;
    line-height: 28rpx;
    text-align: center;
  }
}
</style>
