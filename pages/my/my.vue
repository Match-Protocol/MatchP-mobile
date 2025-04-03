<template>
	<view class="page-bg">
    <u-navbar :is-back="false" class="home-top2" :border-bottom="false" :background="navBackground">
      <image v-if="isMessageReminding" @tap="goNotice" class="inform-icon isInformAnimation" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/is_inform_icon.png" />
      <image v-else @tap="goNotice" class="inform-icon" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/inform_icon.png" />
      <image class="settings-icon2" @tap="scanCodes"
             src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/web3/account_location.png"
      />
      <image class="settings-icon" @tap="goLogOut"
             src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/settings_icon.png"
      />

    </u-navbar>

    <view class="main-container">
		  <view class="main-container-top">
        <!-- 个人信息 -->
        <view class="content-user" @tap.stop="goMy">
          <view class="head">
            <u-image v-if="userInfo.avatar" class="tl-img-116" :src="userInfo.avatar" :loading-icon="headImg"
                     height="140" width="140" border-radius="70" duration="450" :lazy-load="true">
              <u-loading slot="loading">加载中...</u-loading>
            </u-image>
            <!--</block>-->
            <block v-else>
              <!-- <open-data type="userAvatarUrl"></open-data> -->
              <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/head.png" class="tl-img-116"></image>
            </block>
          </view>
          <view class="details" v-if="userInfo.user_uuid">
            <view class="details-item" style="overflow: hidden">
              <view class="tl-font-32-33-w500 tl-width-max-220 tl-text-overflow-1">
                {{ userInfo.nickname}}
              </view>
              <block v-if="userInfo.nickname">
                <image :src="sexImg" class="img-28" />
              </block>
              <!--                <view class="tl-border-8-FF" v-if="authResidentialInfo">-->
              <!--                  <text class="tl-font-24-66 tl-text-overflow-1">{{authResidentialInfo}}</text>-->
              <!--                </view>-->
            </view>

            <view class="tl-did-box">
              <view class="tl-flex-row-center2" @longpress.stop="onCopy(userInfo.user_uuid)">
                <text class="tl-font-20-3d tl-width-max-220 tl-text-overflow-1">{{'did:' + userInfo.user_uuid || '-'}}</text>
              </view>
            </view>
            <view class="tl-label-box">
              <block v-if="userSelectLabel.length > 0">
                <view v-for="(item,index) in userSelectLabel" :key="item.id">{{item.name}}</view>
              </block>
              <view v-else>添加标签</view>
            </view>
          </view>
          <view class="details-login" v-else>去登录</view>
        </view>

        <text class="tl-font-28-00 tl-text-overflow-1">{{userInfo.my_label || '个性签名'}}</text>

        <!--互动模块-->
        <view class="content-interact">
          <view class="content-interact-left">
            <view class="box-left" @tap.stop="goFans(1)">
              关注 <text class="tl-font-bold">{{"&nbsp" + (userInfo.attention || 0)}}</text>
            </view>
            <view class="box-border" @tap.stop="goFans(2)">
              被关注 <text class="tl-font-bold">{{"&nbsp" + (userInfo.by_attention || 0)}}</text>
            </view>
            <view class="box-right" @tap="goCollect">
              收藏 <text class="tl-font-bold">{{"&nbsp" + (userInfo.collect_num || 0)}}</text>
            </view>
          </view>
          <view class="content-interact-right" @tap="goCommunity">
            <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/community.png"/>
            <text class="tl-text-overflow-1">{{authResidentialInfo || '社区未认证'}}</text>
          </view>
        </view>

        <view class="content-class">
          <view class="content-class-item" @tap="goMeatballs">
            <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/my_wz.png"/>
            <view style="display: flex">
              <text class="tl-font-24-ff">玩籽</text>
              <text class="tl-font-28-ff-b">{{"&nbsp" + (userInfo.wz || 0)}}</text>
            </view>
          </view>
          <view class="content-class-line"></view>
          <view class="content-class-item" @tap="goVitality">
            <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/congregate_icon.png"/>
            <view style="display: flex">
              <text class="tl-font-24-ff">汇玩指数</text>
              <text class="tl-font-28-ff-b">{{"&nbsp" + (userInfo.mark || 0)}}</text>
            </view>
          </view>
        </view>
      </view>

      <view class="main-container-bottom">
        <!-- 功能模块 -->
        <view class="content-function">
          <view class="function-item" @tap="goOrderGoods">
            <image class="tl-img-52" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/function/orderForm.png"/>
            <text class="tl-font-28-3d">我的订单</text>
          </view>
          <view class="function-item" @tap="goActivity">
            <image class="tl-img-52" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/function/announce.png"/>
            <text class="tl-font-28-3d">我的发布</text>
          </view>
          <view class="function-item" @tap="goAssetWall">
            <image class="tl-img-52" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/function/asset.png"/>
            <text class="tl-font-28-3d">勋章NFT</text>
          </view>
          <view class="function-item" @tap="goWallet">
            <image class="tl-img-52" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/function/account.png"/>
            <text class="tl-font-28-3d">web3账户</text>
          </view>
        </view>

        <view class="content-community">
          <view v-if="myTribeList.length <= 0" class="community-item">
            <view class="tl-flex-row-start mb-8">
              <image class="tl-img-48" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/tribe.png"/>
              <text class="tl-font-36-45-b">创建社区部落</text>
              <view class="tl-tips" style="margin-left: auto" @tap="goCreateTribe">
                <text>去创建</text>
                <view class="tl-mod">
                  <view class="tl-to_right tl-center-h-v"></view>
                </view>
              </view>
            </view>
            <text class="tl-font-28-45">成为社区主理人/共创者后才能发布活动和场地哦</text>
          </view>
          <swiper v-else :current="swiperCurrentIndex" @animationfinish="animationfinishInfo"
                  @change="changeSwiper" style="height: 240rpx">
            <swiper-item class="swiper-item" v-for="(item, index) in myTribeList" :key="index"
                         @touchstart="itemTouchStart" @touchend="itemTouchEnd">
              <view class="tl-content" @tap.stop="goTribe(item)">
                <view class="tl-flex-row-align-center mb-40">
                  <view class="tl-img-box">
                    <image class="tl-img-112" :src=item.tri_avatar />
                    <!--          0=未加入1=创建者、2=共创员、3=成员-->
                    <view v-if="item.role === 1" class="tl-img-tips">
                      <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/tribe/owner_icon.png" />
                      <text>主理人</text>
                    </view>
                    <view v-else-if="item.role === 2" class="tl-img-tips2">共创者</view>
                  </view>
                  <view class="tl-content-text">
                    <view class="tl-content-title tl-flex-row-start mb-30">
                      <text class="tl-font-32-00 tl-text-overflow-1 tl-max-75">{{item.tribe_name}}</text>
                      <!--have_no_cashout-没有进件信息/未认证 have_cashout-有进件信息/未通过认证 certified-已认证-->
                      <view v-if="item.wx_audit_status === 'certified'" @tap.stop="goAuthentication(item)" class="tl-tips">
                        <image class="tl-img-36" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/authentication_icon.png" />
                        <text>已认证</text>
                      </view>
                      <view v-else-if="item.wx_audit_status === 'have_no_cashout'" @tap.stop="goAuthentication(item)" class="tl-tips">
                        <text>去认证</text>
                        <view class="tl-mod">
                          <view class="tl-to_right tl-center-h-v"></view>
                        </view>
                      </view>
                      <view v-else @tap.stop="goAuthentication(item)" class="tl-tips">
                        <text>认证中</text>
                        <view class="tl-mod">
                          <view class="tl-to_right tl-center-h-v"></view>
                        </view>
                      </view>
                    </view>
                    <text class="tl-font-24-3d">{{item.member_num}}人已加入</text>
                  </view>
                  <!--        审核状态0=未申请、1=待审核、2=审核通过、3=审核不通过-->
                </view>
                <text class="tl-font-28-93 tl-text-overflow-1">{{item.brief || "这个人很懒，什么都没留下"}}</text>
              </view>
            </swiper-item>
          </swiper>
        </view>

        <!--我的任务-->
        <view class="content-task" @tap.stop="goTask">
          <view class="tl-flex-row-bwt">
            <h3 class="tl-font-32-34">周任务/社区任务</h3>
            <view class="sign">
              <button class="sign-btn tl-flex-row-start" :class="isSign?'is-animation':'is-sign-btn'" @tap.stop="!isSign && promptlySignNew()">
                <image class="tl-img-24" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/my/sign_icon.png"/>
                <text class="tl-font-28-45-bold">{{isSign?'已签到':'签到'}}</text>
              </button>
            </view>
          </view>
          <view class="tl-line tl-margin-16-20"></view>
          <view class="task-box">
            <block v-if="taskList && taskList.length > 0">
              <view class="task-box-item tl-flex-row-bwt" v-for="(item,index) in taskList" :key="index">
                <text class="tl-font-24-34">{{item.name}}（{{item.number}}/{{item.max_weekly}}）</text>
                <view class="tl-flex-row-center2">
                  <image class="tl-img-32" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/my_wz.png"></image>
                  <text class="tl-font-24-34 tips-color">+{{item.reward_wz}}</text>
                </view>
              </view>
            </block>

            <view v-else style="text-align: center">
              <text class="tl-font-24-93-center">暂无任务</text>
            </view>
          </view>

        </view>

      </view>
		</view>


    <u-popup v-model="isUpdate" mode="center" width="454rpx"  border-radius="20">
      <view class="tl-up-card">
        <view class="tl-flex-row-center">
          <view class="tl-font-32-333-500 mb-28">更新提示</view>
          <view class="tl-font-24-333">检查到版本有更新，请点击</view>
          <view class="tl-font-24-333">【立即更新】享受更优服务</view>
        </view>
        <view class="tl-flex-row-bwt mt-20">
          <view class="tl-btn-no tl-font-36-org" @click="isUpdate = false">暂不更新</view>
          <view class="tl-btn-yes tl-font-36-org">立即更新</view>
        </view>
      </view>
    </u-popup>

		 <button v-show="!isCheck" class="user-mask" @tap="goLogin1"></button>
    <tabbarCustom :cur-index="4"></tabbarCustom>
	</view>
</template>

<script>
  import tabbarCustom from "../../components/index/tabbar-custom.vue";
  import assetList from "../../components/web3/asset-list.vue";

  export default {
    components: { assetList, tabbarCustom},
		data() {
			return {
        headImg:'https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/head.jpg',
        isUpdate: false, //是否弹出更新提示
				isCheck: false,
				userInfo: {},
				faceUrl: "",
				oldVersion: "", //老版本号
				myVersion: "", //新版本号
				cardStatus: "", // 用户实名认证审核状态 status 审核状态：0-待审核，1-已通过，2-未通过
				sexImg: "", //当前性别的图标
        isSign:'',//是否签到
        taskList:[],//任务中心
        authResidentialInfo:'',//小区
        navBackground:{
          backgroundImage:'linear-gradient( 180deg,#a6c5e9 0%,#b8caee 50%,#d7d2f6 100%)'
        },
        authenticationStatus:'',//认证状态
        auditStatusList:['审核失败', '审核中', '审核通过'],
        contentInfo:'',//认证信息
        userSelectLabel:[],//用户选择的标签
        isMessageReminding:false,
        isShowCommunity:false,//是否显示社区
        myTribeList:[],
			};
		},

		onLoad() {
			//去获取用户是否已经登录状态
			let self = this;

			// let isLogin = uni.getStorageSync("isLogin");
			// console.log("是否登录", isLogin)
			// self.isCheck = isLogin;

			//用户是登录的状态才去接口请求数据
			/*if(isLogin){
			  self.getUserMsg();
			  self.getBaiduFaceUrl();
			}*/
		},
		onShow() {
		},
    // 下拉刷新
    onPullDownRefresh() {
      console.log("下拉刷新")
      this.refreshData();
    },
		methods: {
      refreshData() {
      },
			goLogin1() {
				uni.navigateTo({
				  url: "/pages/login/phoneLogin",
				});
			},
      goNotice() {

      },
      goTribe(item) {

      },
      goAuthentication(item) {

      },
			// 编辑个人资料
			goMy() {
        console.log("去个人资料")

			},
			// 跳转进入关注粉丝
			goFans(num) {
        console.log("去粉丝")

			},
			// 跳转进入玩籽
			goMeatballs() {

			},
      goVitality() {

      },
			// 钱包
			goWallet() {
			},
      goTask() {
      },
			// 活动
			goActivity() {

			},
      goCreateTribe() {

      },
      goAssetWall() {

      },
			// 去社区认证界面
			goCommunity() {
			  //如果已经认证，到展示界面，未认证到选择小区界面
			},
      promptlySignNew() {
      },
      goCollect(){

      },
      goOrderGoods() {

      },
			// 设置
			goLogOut() {

			}
		},
	};
</script>

<style lang="scss" scoped>
	.tl-text-overflow-1 {
    height: auto;
		word-break: break-all;
		text-overflow: ellipsis;
		display: -webkit-box;
		/** 对象作为伸缩盒子模型显示 **/
		-webkit-box-orient: vertical;
		/** 设置或检索伸缩盒对象的子元素的排列方式 **/
		-webkit-line-clamp: 1;
		/** 显示的行数 **/
		overflow: hidden;
		/** 隐藏超出的内容 **/
	}
  .tl-flex-row {
    height: 100%;
    height: 48rpx;
    border-radius: 12rpx;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
  }

	.tl-width-max-220 {
		max-width: 220rpx;
	}
  .tl-width-max-420 {
    max-width: 420rpx;
  }
  .tl-width-max-150 {
    max-width: 150rpx;
  }
  .tl-width-max-460 {
    max-width: 520rpx;
  }
	.tl-img-116 {
		width: 140rpx;
		height: 140rpx;
		border-radius: 50%;
	}
  .tl-img-24-23 {
    width: 24rpx;
    height: 24rpx;
    margin-left: 16rpx;
  }
	.tl-font-32-34 {
		font-size: 32rpx;
		font-family: Source Han Sans SC-Medium, Source Han Sans SC;
		font-weight: 400;
    line-height: 32rpx;
    color: #342A25;
	}

	.tl-font-32-33-w500 {
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: bold;
    font-size: 32rpx;
    color: #000000;
    line-height: 32rpx;
    text-align: left;
	}

	.img-28 {
		width: 28rpx;
		height: 28rpx;
		//border-radius: 16rpx;
		margin-left: 10rpx;
    min-width: 28rpx;
	}

	.tl-border-8-FF {
    width: fit-content;
		height: 32rpx;
		line-height: 32rpx;
		padding: 0 18rpx;
		border-radius: 12rpx;
    background: linear-gradient( 90deg, #21FFAB 0%, #A8FF6D 100%);
    margin-left: 18rpx;
    //background: rgba(255,119,15,0.2);
    opacity: 0.85;
		//border: 2rpx solid #FF770F;
  }

	.tl-font-24-66 {
    float: left;
		font-size: 24rpx;
    font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
		font-weight: 400;
    color: #FEFEFE;
    opacity: 1;
    max-width: 150rpx;
	}
  .tl-font-24-333 {
    font-size: 24rpx;
    font-family: Source Han Sans SC-Normal, Source Han Sans SC;
    font-weight: 400;
    color: #333333;
  }

	.tl-font-24-99 {
		font-size: 24rpx;
		font-family: Source Han Sans SC-Normal, Source Han Sans SC;
		font-weight: 400;
		color: #999999;
	}

	.tl-color-66 {
		color: #666666 !important;
	}

	.tl-font-weight-500 {
		margin-left: 8rpx;
		font-weight: 500;
	}


	.tl-font-28-66 {
		font-size: 28rpx;
		font-family: Source Han Sans SC-Regular, Source Han Sans SC;
		font-weight: 400;
		color: #666666;
	}


	.tl-function-item {
		display: flex;
		// justify-content: center;
		padding-left: 75rpx;
		flex: 5;
	}

	.tl-function-column {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: flex-start;
		margin-left: 20rpx;
	}

	.tl-img-72 {
		width: 64rpx;
		height: 64rpx;
	}

	.tl-img-45 {
		width: 45rpx;
		height: 45rpx;
	}

	.tl-img-49 {
		width: 49rpx;
		height: 49rpx;
	}

	.tl-img-140 {
		width: 170rpx;
		height: 170rpx;
		//margin-left: 77rpx;
		//margin-right: 26rpx;
    position: absolute;
    right: 10rpx;
    top: -20rpx;
	}


	.tl-margin-6 {
		margin-top: 6rpx;
	}

	.tl-font-28-33-w500 {
    font-size: 24rpx;
    font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
    font-weight: 400;
    color: #342A25;
    line-height: 24rpx;
	}



  .tl-isSign-w {
    //width: 150rpx;
    padding: 8rpx;
    border: 2rpx solid #939393;
    border-radius: 12rpx;
  }
  .tl-sign-w {
    //width: 112rpx;
    padding: 12rpx;
    border: 2rpx solid #FF770F;
    border-radius: 12rpx;
  }
  .home-top2 {
    z-index: 2;
    .inform-icon {
      width: 48rpx;
      height: 48rpx;
      min-width: 48rpx;
      margin-right: 40rpx;
      margin-left: 30rpx;
    }
    .settings-icon {
      width: 48rpx;
      height: 48rpx;
      min-width: 48rpx;
    }
    .settings-icon2 {
      width: 40rpx;
      height: 40rpx;
      min-width: 40rpx;
      margin-right: 32rpx;
    }
  }

  .sign-btn {
    background: #DCDCDC;
    position: relative;
    padding: 12rpx 14rpx;
    display: flex;
    align-items: center;
    font-size: 17px;
    font-weight: bold;
    text-decoration: none;
    cursor: pointer;
    //border: 1px solid #FF770F;
    border-radius: 40rpx;
    outline: none;
    overflow: hidden;
    transition: color 0.3s 0.1s ease-out;
    text-align: center;
    z-index: 3;
    //animation: flipInY 0.8s ease forwards;
  }

  //.sign-btn text {
  //  margin: 10px;
  //}
  .is-animation {
    animation: flipInY 0.8s ease forwards;
  }
  .sign-btn::before {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    content: '';
    border-radius: 50%;
    display: block;
    width: 30em;
    height: 30em;
    left: -5em;
    text-align: center;
    transition: box-shadow 0.6s ease-out;
    z-index: -1;
  }

  .is-sign-btn {
    color: #fff;
    //border: 1px solid #FF770F;
    padding: 12rpx 28rpx;
    border:none;
    background: linear-gradient( 90deg, #F9A9F2 0%, #B9FBFF 100%);
  }

  .is-sign-btn::before {
    box-shadow: inset 0 0 0 10em linear-gradient( 90deg, #F9A9F2 0%, #B9FBFF 100%);
  }

  .is-sign-btn>.tl-font-24-ff {
    color: #454545;
  }


  .tl-img-24 {
    width: 32rpx;
    height: 32rpx;
    margin-right: 6rpx;
  }
  .tl-font-28-45 {
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 400;
    font-size: 28rpx;
    color: #454545;
    line-height: 32rpx;
    text-align: left;
  }
  .tl-font-28-45-bold {
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: bold;
    font-size: 28rpx;
    color: #454545;
    line-height: 32rpx;
    text-align: left;
  }
  .tl-font-24-ff {
    font-size: 24rpx;
    line-height: 24rpx;
    font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
    font-weight: 400;
    color: #939393;

    //margin-left: 12rpx;
  }
  .tl-font-28-ff-b {
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: bold;
    font-size: 28rpx;
    color: #FFFFFF;
    line-height: 28rpx;
    text-align: left;
  }
  .tl-font-20-3d {
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 400;
    font-size: 20rpx;
    color: #3D3D3D;
    line-height: 28rpx;
    text-align: left;
  }
  .tl-font-24-93 {
    font-size: 24rpx;
    font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
    font-weight: 400;
    color: #D2D2D2;
    line-height: 24rpx;
  }
  .tl-font-24-93-center {
    font-size: 24rpx;
    font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
    font-weight: 400;
    color: #939393;
    line-height: 24rpx;
    text-align: center;
  }
  .tl-font-40-34 {
    font-size: 28rpx;
    font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
    font-weight: 400;
    color: #FFFFFF;
    line-height: 40rpx;
    margin-left: 8rpx;
  }

  .tl-box-left {
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .tl-box-right {
    position: absolute;
    bottom: 16rpx;
    right: 10rpx;
    width: 100rpx;
    height: 100rpx;
  }
  .tl-box-btn {
    width: 124rpx;
    height: 40rpx;
    background: #FFFFFF;
    border-radius: 12rpx;

  }

  .tl-font-32 {
    font-size: 32rpx;
    font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
    font-weight: 400;
    line-height: 32rpx;
  }
  .tl-font-24 {
    font-size: 24rpx;
    font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
    font-weight: 400;
    line-height: 24rpx;
  }
  .tl-img-11 {
    width: 11rpx;
    height: 11rpx;
    margin-left: 10rpx;
  }
  .tl-color-1a {
    color: #1ABA72;
  }
  .tl-color-ff {
    color: #FF770F;
  }
  .tl-img-144 {
    width: 100rpx;
    height: 100rpx;
  }

  .tl-line {
    width: 622rpx;
    height: 0;
    border: 2rpx solid #D2D2D2;
    opacity:0.2;
  }
  .tl-margin-16-20 {
    margin: 16rpx 0 20rpx;
  }
  .tl-margin-16-32 {
    margin: 16rpx 0 32rpx;
  }

  .tl-img-32 {
    width: 32rpx;
    height: 32rpx;
    margin-right: 16rpx;
  }
  .tl-font-24-34 {
    font-size: 24rpx;
    font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
    font-weight: 400;
    color: #342A25;
    line-height: 24rpx;
  }
  .tips-color {
    color: #1ACDE8;
    font-weight:bold;
  }
  .tl-img-64 {
    width: 64rpx;
    height: 64rpx;
    z-index: 5;
    margin-right: 10rpx;
  }
  .tl-font-32-ff {
    font-size: 32rpx;
    font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
    font-weight: 700;
    color: #454545;
    //color: rgb(37, 37, 37);
    line-height: 32rpx;
    z-index: 5;
  }

	.page-bg {
		width: 100vw;
		min-height: 100vh;
		background-size: 750rpx auto;
		//background-color: #454545;
		position: relative;
    //background: linear-gradient( 180deg, #454545 0%, #454545 45%, #F6F6F6 50%,#F6F6F6 100%);
		.main-container {
      position: relative;
			width: 100%;
			display: flex;
			flex-direction: column;

			.main-container-top {
        position: relative;
        width: 100%;
        height: 466rpx;
        padding-top: 20rpx;
        //background: #454545;
        background: linear-gradient( 180deg, #dbd3f7 10%, #f9ddfe 65%, #f2f3f3 100%);
        .content-user {
          width: 686rpx;
          height: 140rpx;
          display: flex;
          align-items: center;
          margin: 0 auto 40rpx;
          //background: #FFFFFF;
          .head {
            width: 140rpx;
            height: 140rpx;
          }
          .details {
            margin-left: 28rpx;
            //width: 542rpx;
            flex: 1;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: space-between;

            .details-item {
              display: flex;
              align-items: center;
              .sign {
                margin-left: auto;
                //height: 30rpx;
              }
            }

          }
          .details-login {
            margin-left: 28rpx;
            display: flex;
            align-items: center;
            font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
            font-weight: bold;
            font-size: 32rpx;
            color: #000000;
            line-height: 32rpx;
            text-align: left;
          }

        }
        .content-interact {
          width: 686rpx;
          height: 52rpx;
          display: flex;
          justify-content: space-between;
          align-items: center;
          margin: 20rpx auto 24rpx;
          .content-interact-left {
            height: 24rpx;
            flex: 1;
            display: flex;
            align-items: center;
            .box-left {
              width: auto;
              height: 100%;
              padding-right:20rpx;
              font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
              font-weight: 400;
              font-size: 24rpx;
              color: #000000;
              line-height: 24rpx;
              text-align: left;
            }
            .box-border {
              width: auto;
              height: 100%;
              padding: 0 20rpx;
              font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
              font-weight: 400;
              font-size: 24rpx;
              color: #000000;
              line-height: 24rpx;
              text-align: center;
              border-left: 2rpx solid #000000;
              border-right: 2rpx solid #000000;
            }
            .box-right {
              width: auto;
              height: 100%;
              padding-left: 20rpx;
              font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
              font-weight: 400;
              font-size: 24rpx;
              color: #000000;
              line-height: 24rpx;
              text-align: right;
            }
          }
          .content-interact-right {
            width: auto;
            max-width: 32%;
            height: 100%;
            padding: 0 16rpx 0 6rpx;
            background: rgba(60,200,255,0.3);
            border-radius: 40rpx;
            display: flex;
            align-items: center;
            >image {
              width: 40rpx;
              height: 40rpx;
              min-width: 40rpx;
              margin-right: 6rpx;
            }
            >text {
              font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
              font-weight: bold;
              font-size: 24rpx;
              color: #000000;
              line-height: 24rpx;
              text-align: left;
            }
          }
        }
        .content-class {
          position: absolute;
          left: 50%;
          transform: translateX(-50%);
          bottom: 40rpx;
          width: 686rpx;
          height: 80rpx;
          margin: auto;
          background: linear-gradient( 180deg, #757575 0%, #454545 100%);
          border-top-left-radius: 20rpx;
          border-top-right-radius: 20rpx;
          display: flex;
          justify-content: center;
          align-items: center;
          .content-class-item {
            flex: 1;
            height: 100%;
            display: flex;
            align-items: center;
            padding-left: 40rpx;
            >image {
              width: 32rpx;
              height: 32rpx;
              min-width: 32rpx;
              margin-right: 20rpx;
            }
          }
          .content-class-line {
            width: 0rpx;
            height: 40rpx;
            border: 2rpx solid rgba(0,0,0,0.1);
          }
        }
      }
      .main-container-bottom {
        position: relative;
        top: -40rpx;
        width: 100%;
        height: auto;
        background: linear-gradient( 180deg, #FFFFFF 0%, rgba(255,255,255,0) 40%);
        //background: #F6F6F6;
        border-top-left-radius: 24rpx;
        border-top-right-radius: 24rpx;
        padding-top: 40rpx;
        .content-tribe {
          width: 686rpx;
          height: 160rpx;
          display: flex;
          justify-content: space-between;
          //background: #FFFFFF;
          margin: 0 auto 32rpx;
          .medal-box {
            position: relative;
            width: 326rpx;
            height: 100%;
            padding: 16rpx 8rpx 16rpx 16rpx;
            border-radius: 12rpx;
            background: #B6F5D7;
          }
          .tribe-box {
            position: relative;
            width: 326rpx;
            height: 100%;
            padding: 16rpx 8rpx 16rpx 16rpx;
            border-radius: 12rpx;
            background: #FFEAB6;
          }
        }

        .content-function {
          margin: 0 auto 42rpx;
          width: 686rpx;
          height: auto;
          padding: 0 22rpx;
          display: flex;
          justify-content: space-between;
          align-items: center;
          .function-item {
            width: auto;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            align-items: center;
          }
        }

        .content-my-activity {
          width: 686rpx;
          height: 112rpx;
          padding-left: 32rpx;
          display: flex;
          justify-content: space-between;
          align-items: center;
          background: #FFFFFF;
          margin: 0 auto 32rpx;
          overflow: hidden;
          position: relative;
          border-radius: 12rpx;
        }
        .content-task {
          width: 686rpx;
          margin: 0 auto 32rpx;
          padding: 32rpx;
          background: #FFFFFF;
          border-radius: 20rpx;
          .task-box-item {
            margin-bottom: 32rpx;
          }
          .task-box-item:last-child {
            margin-bottom: 0;
          }
        }

        .content-service {
          width: 686rpx;
          height: 340rpx;
          padding: 32rpx;
          margin: auto;
          display: flex;
          flex-direction: column;
          background: #FFFFFF;
          border-radius: 12rpx;
          .service-box {
            width: 100%;
            height: 100%;
            display: flex;
            flex-wrap: wrap;
            align-content: space-between;

            .service-box-item {
              width: 25%;
              display: flex;
              flex-direction: column;
              align-items: center;
            }
          }
        }
        .content-community {
          width: 686rpx;
          margin: 0 auto 32rpx;
          .community-item {
            width: 100%;
            height: 160rpx;
            padding: 30rpx;
            background: #FFFFFF;
            border-radius: 24rpx;
          }
          .swiper-item {
            width: 100%;
          }
        }
      }
		}

		.publish {
			position: fixed;
      width: 176rpx;
      height: 80rpx;
      //background: linear-gradient(270deg, #FF770F 0%, #FFA865 100%);
			right: 20rpx;
			bottom: 245rpx;
			//border-radius: 40rpx;
			font-size: 28rpx;
			font-family: Source Han Sans SC-Medium, Source Han Sans SC;
			font-weight: 500;
			//padding: 8rpx 24rpx 8rpx 8rpx;
      display: flex;
      justify-content: space-between;
      align-items: center;
		}
	}
  .tl-did-box {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    >view {
      width: auto;
      height: 32rpx;
      background: rgba(69,69,69,0.1);
      border-radius: 8rpx;
      padding: 0 15rpx;
    }
  }
  .tl-label-box {
    width: 100%;
    height: auto;
    display: flex;
    align-items: center;
    >view {
      width: auto;
      height: 40rpx;
      padding: 0 10rpx;
      border-radius: 12rpx;
      border: 2rpx solid rgba(0,0,0,0.3);
      font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
      font-weight: bold;
      font-size: 24rpx;
      color: #454545;
      text-align: center;
      margin-right: 8rpx;
      display: flex;
      align-items: center;
    }
    view:last-child {
      margin-right: 0;
    }
  }
.tl-up-card{
  padding: 34rpx 16rpx;
}
.tl-flex-row-center{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.tl-img-52 {
  width: 52rpx;
  height: 52rpx;
  min-width: 52rpx;
  margin-bottom: 24rpx;
}
.tl-font-28-3d {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 400;
  font-size: 28rpx;
  color: #3D3D3D;
  line-height: 28rpx;
  text-align: left;
}
.mt-20{
  margin-top: 20rpx;
}
.tl-font-bold {
  font-weight: bold;
}
.tl-btn-no{
  width: 146rpx;
  height: 52rpx;
  line-height: 52rpx;
  text-align: center;
  border-radius: 10rpx 10rpx 10rpx 10rpx;
  border: 2rpx solid #323333;
  font-size: 28rpx;
  font-family: Source Han Sans SC-Medium, Source Han Sans SC;
  font-weight: 500;
  color: #333333;
}
  .tl-btn-yes{
    width: 146rpx;
    height: 52rpx;
    line-height: 52rpx;
    text-align: center;
    border-radius: 10rpx 10rpx 10rpx 10rpx;
    border: 2rpx solid #323333;
    font-size: 28rpx;
    font-family: Source Han Sans SC-Medium, Source Han Sans SC;
    font-weight: 500;
    color: #FF770F;
  }

	/*手写样式文件=====================*/
	.user-mask {
		width: 100vw;
		height: 100vh;
		position: absolute;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		z-index: 998;
		opacity: 0;
		border: 0 !important;
	}





  .button {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    //padding: 15px 30px;
    //padding: 8rpx 14rpx 8rpx 8rpx;
    padding: 8rpx 20rpx 8rpx 8rpx;
    border: 0;
    position: relative;
    overflow: hidden;
    border-radius: 40rpx;
    transition: all 0.02s;
    font-weight: bold;
    color: rgb(37, 37, 37);
    z-index: 0;
    box-shadow: 0 0px 7px -5px rgba(0, 0, 0, 0.5);
  }

  //.button:hover {
  //  background: rgb(193, 228, 248);
  //  color: rgb(33, 0, 85);
  //}

  .button:active {
    transform: scale(0.97);
  }

  .hoverEffect {
    position: absolute;
    bottom: 0;
    top: 0;
    left: 0;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1;
    border-radius: 40rpx;
  }

  .hoverEffect view {
    //background: rgb(222,0,75);
    background: rgb(255, 119, 15);
    //background: linear-gradient(90deg, rgba(241, 39, 17,1) 0%, rgba(255, 119, 15,1) 49%, rgba(245, 175, 25,1) 100%);
    //background: linear-gradient(90deg, rgba(248, 54, 0,1) 0%, rgba(255, 119, 15,1) 100%);
    //background: linear-gradient(90deg, rgba(64, 224, 208,0.8) 0%, rgba(255, 119, 15,1) 49%, rgba(255, 0, 128,0.8) 100%);
    //background: linear-gradient(90deg, rgba(33,255,171,1) 0%, rgba(135, 255, 139,1) 55%, rgba(233,255,109,1) 100%);
    background: linear-gradient(90deg, rgba(33,255,171,1) 0%, rgba(233,255,109,1) 100%);
    border-radius: 40px;
    //width: 10rem;
    //height: 10rem;
    width: 176px;
    height: 80px;
    //transition: 0.4s;
    filter: blur(20px);
    animation: effect infinite 4s linear;
    opacity: 0.8;
  }
  .tl-font-28-00 {
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 400;
    font-size: 28rpx;
    color: #000000;
    line-height: 36rpx;
    text-align: left;
    display: inline-block;
    margin-left: 38rpx;
    white-space: nowrap;
    max-width: 80%;
  }
  //.button:hover .hoverEffect view {
  //  //width: 8rem;
  //  //height: 8rem;
  //  width: 40rpx;
  //  height: 40rpx;
  //}
  .tl-content {
    width: 100%;
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
  .tl-font-32-00 {
    font-size: 32rpx;
    font-family: PingFang SC, PingFang SC;
    font-weight: 400;
    color: #000000;
  }
  .tl-max-75 {
    max-width: 70%;
  }
  .tl-font-24-3d {
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 400;
    font-size: 24rpx;
    color: #3D3D3D;
    line-height: 24rpx;
    text-align: left;
  }
  .tl-font-28-93 {
    font-family: Source Han Sans, Source Han Sans;
    font-weight: 400;
    font-size: 28rpx;
    color: rgba(147,147,147,0.8);
    line-height: 28rpx;
    text-align: left;
  }
  /*空心右箭头*/
  .tl-mod {
    display: flex;
    width: 16rpx;
    height: 16rpx;
    margin-left: 12rpx;
  }
  .tl-center-h-v {
    margin: auto;
  }
  /*空心右箭头*/
  .tl-to_right {
    width: 16rpx;
    height: 16rpx;
    transform: rotate(45deg);
    border-right: 4rpx solid #454545;
    border-top: 4rpx solid #454545;
  }
  .tl-tips {
    //width: 154rpx;
    height: 52rpx;
    display: flex;
    justify-content: center;
    align-items: center;
    background: linear-gradient( 90deg, #F9A9F2 0%, #B9FBFF 100%);
    border-radius: 40rpx;
    margin-left: auto;
    padding: 0 18rpx;
    .tl-img-36 {
      width: 34rpx;
      height: 34rpx;
      min-width: 34rpx;
      margin-right: 6rpx;
    }
    >text {
      font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
      font-weight: bold;
      font-size: 28rpx;
      color: #454545;
      line-height: 28rpx;
      text-align: left;
    }
  }
  .tl-font-36-45-b {
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: bold;
    font-size: 36rpx;
    color: #454545;
    line-height: 32rpx;
    text-align: left;
  }
  .tl-img-48 {
    width: 48rpx;
    height: 48rpx;
    min-width: 48rpx;
    margin-right: 10rpx;
  }
  @keyframes effect {

    0% {
      transform: rotate(0deg);
    }

    100% {
      transform: rotate(360deg);
    }
  }
  button::after{ border: none;}
</style>
