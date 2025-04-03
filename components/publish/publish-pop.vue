<template>
	<u-popup class="pub-pop" v-model="isShortTerm" mode="bottom" border-radius="10" :height="setHeight">
		<view class="popup-box">
			<image class="icon" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/pop-icon.png"></image>
			<view class="popup-container">
				<slot></slot>
			</view>
		</view>
	</u-popup>
</template>

<script>
	export default {
    options: {
      styleIsolation: 'shared', // 解除样式隔离
    },
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
				default: '500rpx'
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
		}
	};
</script>

<style lang="scss" scoped>
	//弹窗相关
	::v-deep .u-drawer-bottom {
		background-color: rgba(98, 98, 98, 0.3) !important;
	}

	.popup-box {
		position: absolute;
		bottom: 0;
		width: 100%;
		height: 90%;
		background: #FFFFFF;
    border-top-right-radius: 30rpx;
    border-top-left-radius: 30rpx;
		.icon {
			position: absolute;
			top: -44rpx;
			left: 16rpx;
			width: 190rpx;
			height: 190rpx;
		}
		.popup-container {
			width: 100%;
			height: 100%;
			padding: 20rpx 32rpx 32rpx;
		}
	}
</style>