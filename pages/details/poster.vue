<template>
	<view class="page-bg">
    <u-navbar :title="'海报模版'" title-color="#FFFFFF" :border-bottom="false"
              :background="navBackground" back-icon-color="#FFFFFF"></u-navbar>
		<view class="main-container">
			<view :class="[isShow ? 'tl-row tl-all': 'tl-row']">
				<block v-for="(item,index) in classList" :key="index">
					<view class="tl-tag" @tap="clickTag(index)" :class="[currentIndex == index ? 'u-b-b-subject-square-select' : 'u-b-b-subject-square']">
						{{item.name}}</view>
				</block>
			</view>
			<!--			<view class="tl-mgs" @tap="toChange">
				<view v-if="!isShow"> 展开<image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/xiala.png" class="tl-img-16"></image></view>
				<view v-else> 收起<image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/xiala.png" class="tl-img-16"></image></view>
			</view>-->

			<view class="tl-flex-row-start" style="flex-wrap: wrap">
        <view class="tl-section" v-for="(item,index) in posterList" :key="index">
<!--          <view class="tl-p-r" >-->
            <!--<image :src="item.url" class="tl-img-686"></image>-->
            <u-image @tap="checkPoster(index)" class="tl-img-332" :src="item.url"
                     :loading-icon="loadingImg" height="234" width="332" duration="450" :lazy-load="true"
                     border-radius="12">
              <u-loading slot="loading"></u-loading>
            </u-image>
            <block v-if="item.isCheck">
              <view class="tl-circle-exclamation tl-check"></view>
<!--              <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/reach.png" class="tl-img-64 tl-check"></image>-->
            </block>
<!--          </view>-->
        </view>
        <block v-if="posterList.length == 0 || !posterList">
          <view class="tl-section-2">
            <view class="tl-img-nodata tl-center">
              <text class="tl-font-nodata">暂无封面</text>
            </view>
          </view>
        </block>
      </view>
		</view>

		<view class="tl-footer">
			<button class="tl-footer-gradual-btn" @tap="saveData">确定</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
        navBackground:{
          background: '#222222'
        },
        loadingImg:'https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/img_loading2.jpg',
				dayList: [{}, {}, {}],
				isChoice: '2',
				currentIndex: 0,
				isShow: false,
				classList: [], //分类列表
				posterList: [], //海报列表
				page: 1,
				classify_id: 1,
				noMoreData: false,
				myUrl: '',

			}
		},




		onLoad() {
			let self = this;
			self.getActClassifyIndex();
			self.getPoster();
		},

		//上拉触底函数
		onReachBottom() {
			this.$log('AAAAAAA用户上拉触底', this.noMoreData)
			if (!this.noMoreData) { //此处判断，上锁，防止重复请求
				//this.noMoreData=true
				this.getPoster()
			}
		},


		methods: {

			//查询当前的海报分类列表
			getActClassifyIndex() {
				this.$http.get('getClassifyIndex', '').then((res, error) => {
					if (res.data.code == 200) {
						let data = res.data.data;
						this.classList = data;
						this.$log('列表', this.classList)
						//this.cateLists = this.formCate(data.cate)
					} else {
						this.$common.toast(res.data.msg)
					}
				})
			},

			//获取海报列表操作
			getPoster() {
				let self = this;
				if (self.noMoreData) return false
				let pageSize = 10;
				let page = self.page;

				let params = {
					id: self.classify_id,
					// page: page,
				}
				self.$http.get('getPoster', params).then((res, error) => {
					if (res.data.code == 200) {
						let list = res.data.data;
						if (list && list.length > 0) {
							let tempArr = [];
							for (let item of list) {
								let obj = {
									id: item.id,
									classify_id: item.activity_type_id,
									url: item.url,
									isCheck: false,
								}
								tempArr.push(obj)
							}
							self.posterList.push(...tempArr)
              this.$log("封面模版",self.posterList)
							//更新各参数
							self.noMoreData = list.length < pageSize; // true 说明没有更多数据了
							self.page = list.length == pageSize ? ++page : page;
						} else {
							// self.posterList = [];
						}
					} else {
						this.$common.toast(res.data.msg)
					}
				})
			},


			//是否选中海报操作的函数：
			checkPoster(index) {

				//置空已选中的操作
				let list = this.posterList;
				for (let item of list) {
					item['isCheck'] = false;
				}

				let myIndex = index;
				this.posterList[index].isCheck = true;
				this.myUrl = this.posterList[index].url; //获取选中海报的url
        this.$log("选中海报",this.posterList[index])
			},

			//确实海报选中
			saveData() {
				if (this.myUrl.length == 0) {
					this.$common.toast('请先勾选海报')
					return
				}
				let pages = getCurrentPages(); //获取所有页面栈实例列表
				let nowPage = pages[pages.length - 1]; //当前页页面实例
				let prevPage = pages[pages.length - 2]; //上一页页面实例
        this.$log("海报选中",prevPage)
				// prevPage.$vm.imgUrl = this.myUrl; //修改上一页data里面的海报的imgUrl参数
        this.$set(prevPage.$vm.coverUrlList[0],'path',this.myUrl);
				uni.navigateBack({ //uni.navigateTo跳转的返回，默认1为返回上一级
					delta: 1
				});
			},



			clickTag(index) {
				this.$log('aaaaa', index)
				this.currentIndex = index;
				this.classify_id = this.classList[index].id;
				this.$log('this.classify_id==', this.classify_id)
				this.page = 1;
				this.noMoreData = false;
				this.posterList = []; //重置数据为空
				this.getPoster();
			},
			toChange() {
				this.isShow = !this.isShow;
			},
		}
	}
</script>

<style lang="less">
	.page-bg {
		width: 100vw;
		height: auto;
    min-height: 100vh;
		overflow: hidden;
		background-size: 750rpx auto;
		background-color: #222222;
	}

	.main-container {
		width: 710rpx;
		margin: 0 auto;
		display: flex;
		flex-direction: column;
		/*width: 686rpx;
		height: 858rpx;
		background: #FFFFFF;
		border-radius: 8rpx;
		margin-top: 64rpx;
		padding: 32rpx 56rpx;*/
	}

	.tl-img-76 {
		width: 76rpx;
		height: 76rpx;
	}

	.tl-row {
		display: flex;
		/*justify-content: space-around;*/
		justify-content: space-around;
		align-items: center;
		flex-wrap: wrap;
		//height: 200rpx;
		overflow: hidden;
		transition: height 3s;
    margin-bottom: 42rpx;
	}

	.tl-all {
		height: auto !important;
	}

	.tl-row:after {
		content: "";
		flex: auto;
	}

	.tl-tag {
		width: 160rpx;
		height: 68rpx;
		line-height: 68rpx;
		background: #F7F7F7;
		border-radius: 12rpx;
		text-align: center;
		//margin: 0 16rpx 20rpx 0;
    margin: 0 8rpx 20rpx;
		overflow: hidden;
	}

	.tl-section {
    position: relative;
    margin: 0 11rpx 30rpx;
	}

	.tl-p-r {
		position: relative;
		//height: 448rpx;
    margin-bottom: 30rpx;
	}

	.tl-img-332 {
    display: block;
    margin: auto;
		width: 332rpx;
		height: 234rpx;
    border-radius: 12rpx;
	}

	.tl-img-64 {
		width: 40rpx;
		height: 40rpx;
    min-width: 40rpx;
	}


	.tl-check {
		position: absolute;
		right: 12rpx;
		top: 10rpx;
	}

	.tl-img-16 {
		width: 24rpx;
		height: 24rpx;
		vertical-align: middle;
	}

	.tl-mgs {
		display: flex;
		justify-content: center;
		align-items: center;
	}
  .tl-footer {
    width: 100%;
    height: 176rpx;
    z-index: 9;
    position: fixed;
    bottom: 0;
    padding: 0 32rpx;
    background: #222222;
    display: flex;
    justify-content: center;
    align-items: center;
    box-shadow: none;
  }
  .tl-circle-exclamation {
    width: 40rpx;
    height: 40rpx;
    min-width: 40rpx;
    border-radius: 50%;
    display: inline-block;
    background-color: #1ACDE8;
  }
  .tl-circle-exclamation::before {
    content: "√";
    font-size: 40rpx;
    color: #454545;
    line-height: 40rpx;
    text-align: center;
    display: block;
  }
</style>
