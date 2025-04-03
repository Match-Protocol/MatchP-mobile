<template>
	<view class="tl-home-width container" :style="{top:setTop,width:setWidth,height:setHeight,borderRadius:setBorderRadius}"
        :class="curType == 0?'is-border-shadow':''">
		<scroll-view class="activity-lable" scroll-x="true" show-scrollbar="false" enable-flex="true" @scroll="onScroll">
			<block v-for="(item,index) in activitys" :key="index">
				<view class="activity-item" @tap="onActivityItem(index)">
					<view class="activity-item-box tl-text-gradual">
            <text :class="item.select?'tl-secTitle':''"></text>
            <image v-if="item.thumb" class="activity-image" :style="{width:setImg,height:setImg,minWidth:setImg}" :src="item.thumb"></image>
            <text class="activity-text" :class="[item.select?'tl-activity-text':'',item.thumb?'tl-activity-left':'']">{{item.title}}</text>
          </view>
				</view>
				<!-- <image class="activity-image" :src="item.img"></image>
					<text class="activity-text">{{item.text}}</text> -->
			</block>
		</scroll-view>

    <view class="tl-shade"></view>
	</view>
</template>

<script>

  export default {
		data() {
			return {
				type:[{
          image:'active-image',
          text:'active-text',
        },{
          image:'place-image',
          text:'place-text',
        }]
			};
		},
		props: {
			activitys: {
				type: Array,
				default: []
			},
      curType:{
        type: Number,
        default: 0
      },
			setTop: {
				type: String,
				default: "-26rpx"
			},
      setWidth:{
        type: String,
        default: "686rpx"
      },
      setHeight:{
        type: String,
        default: "50rpx"
      },
      setImg:{
        type: String,
        default: "36rpx"
      },
      setFontSize:{
        type: String,
        default: "32rpx"
      },
      setBorderRadius:{
        type: String,
        default: "0"
      }
		},
		methods: {
			onActivityItem(e) {
				console.log("onActivityItem", e)
				this.$emit("onChangeaCtivity",e)
			},
			onMoreActivity() { //更多活动

			},
			onScroll(e) {
				// console.log("onScroll", e)
			}
		}
	}
</script>

<style scoped lang="scss">
	.tl-home-width {
		//position: relative;
		////width: 686rpx;
		//// top: -46rpx;
		//left: 50%;
		//transform: translateX(-50%);
	}
  @keyframes dynamic {
    0% {
      width: 50rpx;
      height: 50rpx;
    }
    25% {
      width: 70rpx;
      height: 70rpx;
    }
    50% {
      width: 80rpx;
      height: 80rpx;
    }
    75% {
      width: 75rpx;
      height: 75rpx;
    }
    100% {
      width: 70rpx;
      height: 70rpx;
    }


    //0% {
    //  left: 50%;
    //  transform: translate(-50%,-20rpx);
    //}
    //50% {
    //  left: 50%;
    //  transform: translate(-50%,20rpx),scale(1.5);
    //}
    //100% {
    //  left: 50%;
    //  transform: translate(-50%,0rpx);
    //  width: 80rpx;
    //  height: 80rpx;
    //}
  }
  @keyframes dynamic1 {
    0% {
      width: 84rpx;
      height: 84rpx;
    }
    25% {
      width: 104rpx;
      height: 104rpx;
    }
    50% {
      width: 124rpx;
      height: 124rpx;
    }
    75% {
      width: 119rpx;
      height: 119rpx;
    }
    100% {
      width: 114rpx;
      height: 114rpx;
    }
  }
  .is-border-shadow {
    box-shadow: 0rpx 4rpx 12rpx 2rpx rgba(0, 0, 0, 0.11);
  }
	.container {
		//height: 50rpx;
		//border-radius: 15rpx;
		//background: #FEFEFE;
    background: linear-gradient( 180deg, #FFFFFF 0%, #fcfcfc 100%);
		border-radius: 20rpx;
    margin: 0 auto 36rpx;
    position: relative;
		.activity-lable {
			width: 100%;
			height: 100%;
			white-space: nowrap;
			.activity-item {
				//width: 137.2rpx;
        //width: calc(20%);
				height: 100%;
				display: inline-block;
        position: relative;
        perspective: 1000;
        margin-right: 30rpx;
        &:last-child {
          margin-right: 0;
        }
        .activity-item-box {
          display: flex;
          .activity-image {
            //display: block;
            position: absolute;
            top: 50%;
            transform: translateY(-50%);
            //margin: 7rpx 4rpx 7rpx 0;
          }
          .activity-text {
            //position:absolute;
            //bottom: 0;
            display: block;
            width: 100%;
            //font-family: myFont;
            font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
            font-weight: bold;
            font-size: 32rpx;
            color: #767676;
            line-height: 50rpx;
            text-align: left;
            z-index: 1;
          }
          .active-image {
            margin: 20rpx auto 0;
            animation: dynamic 0.6s ease forwards;
          }
          .active-text {
            bottom: -10rpx;
            font-size: 32rpx !important;
            font-family: myFont;
            font-weight: 400;
            color: #333333;
            animation: swing 0.8s ease forwards;
          }
          .place-image {
            margin: 20rpx auto 0;
            //width: 60rpx;
            //height: 60rpx;
            animation: dynamic1 0.6s ease forwards;
          }
          .place-text {
            bottom: -7rpx;
            font-size: 36rpx !important;
            font-family: myFont;
            font-weight: 400;
            color: #333333;
            animation: swing 0.8s ease forwards;
          }
        }
			}
		}
	}
  .tl-shade {
    position: absolute;
    top: 0;
    right: 0;
    width: 148rpx;
    height: 100%;
    //background: pink;
    pointer-events: none;//点击穿透
    background: linear-gradient( 90deg, rgba(255,255,255,0) 0%, rgba(255,255,255,0.8) 40%, #FFFFFF 100%);
  }
  .tl-activity-text {
    font-family: myFont !important;
    font-weight: 400 !important;
    color: #342A25 !important;
    font-size: 42rpx !important;
  }
  .tl-activity-left {
    padding-left: 40rpx;
  }
  .tl-animation {
    //flipInX  jackInTheBox
    animation: slideInDown 0.5s ease forwards;
  }
</style>