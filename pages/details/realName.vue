<template>
	<view class="page-bg">
		<view class="main-container">
			<view class="content-card">
				<view class="card-info">
					<view class="info-left">
						<image :src="activityInfo.picture_url" class="tl-img-272"></image>
            <!--mode="widthFix"-->
					</view>
					<view class="info-right">
						<h2 class="tl-h2-32 tl-font-weight-bold">{{ activityInfo.theme }}</h2>
            <view class="iconfont-size">
              <image class="image-24-border" :src="activityInfo.tri_avatar"></image>
              <span class="tl-font-20-gray" style="max-width: 60%">{{activityInfo.tribe_name}}</span>
              <typeTips :name="tipsType[activityInfo.label - 1]" :setSize="20" style="margin-left: 8rpx"></typeTips>
            </view>
						<view class="iconfont-size">
							<image class="image-24" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/time.png"></image>
							<span class="tl-font-20-gray">{{startTime}}-{{endTime}}</span>
						</view>
						<view class="iconfont-size address-distance">
							<image class="image-24" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/location.png"></image>
							<span v-if="activityInfo.address" class="tl-font-20-gray">{{ activityInfo.address }}</span>
              <span v-else class="tl-font-20-gray">线上活动</span>
						</view>
					</view>
				</view>
				<!--分割线-->
				<view class="tl-cut-off-rule"></view>
				<view class="card-tiem">
					<text class="tl-font-32-333333">报名人数</text>
					<text class="tl-font-24-999999">(剩余
						<text class="tl-color-currency">{{surplusQuota}}</text>
						张)
					</text>
					<view class="info-input tl-row-ultimate">
						<u-number-box v-model="applyCount" @change="onApplyCountChange" :min="1"
							:max="grouping.attendance - grouping.registration_population" :input-width="60"></u-number-box>
					</view>
				</view>
				<view class="card-tiem">
					<text class="tl-font-32-333333">报名金额</text>
					<text class="tl-row-ultimate">
<!--            <text class="tl-font-32-00-500">￥{{activityInfo.collect_fee}}/人</text>-->
						<text class="tl-font-32-00-500">￥{{grouping.collect_fee}}/人</text>
					</text>
				</view>
<!--        <view class="card-tiem tl-flex-row-bwt">-->
<!--          <text class="tl-font-32-333333">隐藏我的信息</text>-->
<!--&lt;!&ndash;          <view class="tl-row-ultimate" style="width: 250rpx;">&ndash;&gt;-->
<!--&lt;!&ndash;            <u-input class="input" v-model="applyName" type="text" border="border" placeholder="伙伴怎么称呼你"&ndash;&gt;-->
<!--&lt;!&ndash;                     maxlength="20" height="48" border-color="#FFFFFF" input-align="right" />&ndash;&gt;-->
<!--&lt;!&ndash;          </view>&ndash;&gt;-->
<!--          <u-switch v-model="isHide" active-color="#1ACDE8" inactive-color="#D2D2D2" size="40" vibrate-short='true'-->
<!--                    ></u-switch>-->
<!--        </view>-->

<!--        <view v-if="questionnaire" class="card-tiem2 tl-flex-row-bwt" style="margin-bottom: 0" @tap="goCollection">-->
<!--          <text class="tl-font-32-333333">信息收集<text class="tl-tipe-red">*</text></text>-->
<!--          <image class="tl-img-32" src="../../static/my/my_right.png"></image>-->
<!--        </view>-->

        <view class="tl-cut-off-rule"></view>

        <view class="card-tiem">
          <text class="tl-font-32-333333">支付方式</text>
          <view class="tl-row-ultimate" @tap="shareToggle">
            <view class="tl-pay-item" v-if="isWechatPay">
              <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/pay/weixin.jpg"></image>
              <text class="tl-margin-8">微信</text>
            </view>
            <view class="tl-pay-item" v-else>
              <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/pay/zhifubao.jpg"></image>
              <text class="tl-margin-8">支付宝</text>
            </view>
          </view>
        </view>
        <view v-if="memberDiscount.discount" class="card-tiem">
          <text class="tl-font-32-333333">数字会员折扣</text>
            <view class="tl-pay-item2">
              <assetIcon :iconUrl="memberDiscount.picture"
                         :type="assetType" :setSize="assetType === 1?22:32"></assetIcon>
              <text style="margin-left: 6rpx" class="tl-font-24-999999">{{memberDiscount.digital_name}}(折扣{{memberDiscount.discount * 10}})</text>
            </view>
        </view>
				<view class="card-tiem" v-if="grouping.collect_wz_money && grouping.collect_fee > 0">
					<text class="tl-font-32-333333">使用玩籽</text>
<!--					<text class="tl-font-24-999999">({{userInfo.wz}}玩籽)</text>-->

          <view class="tl-row-ultimate tl-flex-row-start">
            <text class="tl-font-24-999999" style="margin-right: 10rpx">
              (可消耗{{deductionCurrencySub}}玩籽抵扣<text class="tl-color-currency">￥{{deductionSub}}</text>)
            </text>
            <u-checkbox-group >
              <u-checkbox @change="onUseCoin" v-model="isUseCoin" shape="circle"
                          active-color="#42D3AD"></u-checkbox>
            </u-checkbox-group>
          </view>
				</view>

        <view class="tl-cut-off-rule"></view>

        <view class="card-tiem">
          <text class="tl-font-32-333333">合计</text>
          <text class="tl-row-ultimate">
            <text class="tl-font-32-00-500">￥{{amount}}</text>
          </text>
        </view>


			</view>
			<!-- 说明相关 -->
			<view class="tl-explain">
				<h2 class="tl-h2-32">退票说明</h2>
        <ul class="tl-font-28-66">
          <block v-if="Number(activityInfo.refund_model_id === 5)">
            <li>1.此活动支付后不可取消和退款</li>
            <li>2.因不可抗力因素导致活动无法正常进行可无条件退款</li>
          </block>
          <block v-else>
            <li>1.距活动开始{{cancelList[activityInfo.refund_model_id]}}可全额退款</li>
            <li v-if="Number(activityInfo.refund_model_id) === 0">2.距离活动开始时请与主理人协商退款</li>
            <li v-else>2.距离活动开始小于{{canceNumlList[activityInfo.refund_model_id]}}小时请与主理人协商退款</li>
            <li>3.因不可抗力因素导致活动无法正常进行可以无条件退款</li>
          </block>
        </ul>
			</view>
			<!--<view class="tl-explain">-->
			<!--	<h2 class="tl-h2-32">有保险，更放心</h2>-->
			<!--	<ul class="tl-font-28-66">-->
			<!--		<li>1.距活动开始前12小时取消可全额退款</li>-->
			<!--		<li>2.距离活动开始小于12小时请与主理人协商退款</li>-->
			<!--	</ul>-->
			<!--</view>-->
			<view class="tl-explain">
				<h2 class="tl-h2-32">免责申明</h2>
				<ul class="tl-font-28-66">
					<li>因活动造成的意外损伤风险自担，平台及活动组织方无任何责任，报名成功视为自愿受本声明约束。</li>
				</ul>
			</view>

      <view class="tl-h176"></view>

		</view>
    <!--支付按钮-->
    <view class="content-footer">
      <view class="footer-left">
        <text class="tl-font-28-33">合计
          <text class="tl-color-currency tl-font-28-33">￥</text>
          <text class="tl-color-currency tl-font-32-ff">{{amount}}</text>
          <text v-show="isDeduction || memberDiscount.discount" class="tl-color-currency tl-font-24-33">(共抵扣￥{{allDeduction}})</text>
        </text>
      </view>
      <view>
        <!--&lt;!&ndash;先判断活动是否需要实名&ndash;&gt;-->
        <!--<block v-if="activityInfo.apply_type">-->
        <!--	&lt;!&ndash;然后判断当前用户是否已经实名了&ndash;&gt;-->
        <!--	<block v-if="userInfo.identity_auth_status">-->
        <!--		<button class="footer-right u-c-subject u-b-b-subject" @tap="saveIsOrder">立即支付</button>-->
        <!--	</block>-->
        <!--	<block v-else>-->
        <!--		<button class="footer-right u-c-subject u-b-b-subject" @tap="goFace2">立即支付</button>-->
        <!--	</block>-->
        <!--</block>-->
        <!--<block v-else>-->
        <!--	<button class="footer-right u-c-subject u-b-b-subject" @tap="saveIsOrder">立即支付</button>-->
        <!--</block>-->
        <button class="footer-right u-b-b-subject-gradual" @tap="saveIsOrder">立即支付</button>
      </view>
    </view>

    <!-- 弹窗相关 -->
    <!-- 支付弹窗 -->
    <u-popup v-model="isPayPop" mode="bottom" border-radius="20" height="500rpx">
      <view class="tl-payPop">
        <view class="tl-pay-pop-item" @tap="onChangePay(0)">
          <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/pay/weixin.jpg"></image>
          <text>微信</text>
        </view>
        <view class="tl-pay-pop-item" @tap="onChangePay(1)">
          <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/pay/zhifubao.jpg"></image>
          <text>支付宝</text>
        </view>
      </view>
    </u-popup>
    <!--问卷弹窗-->
    <u-popup v-model="isQuestionnaireTips" mode="center" width="654rpx" height="auto" border-radius="22">
      <view class="tl-up-card">
        <view class="tl-flex-row-center mb-64">
          <view class="tl-font-32-333-500">您已成功报名此活动 请填写问卷信息，谢谢配合</view>
        </view>
        <view class="tl-flex-row-bwt">
          <view class="tl-btn-yes u-b-b-subject-border-cancel" @tap="cancelQuestionnaire">取消</view>
          <view class="tl-btn-no u-b-b-subject-border" @tap="goQuestionnaire">确定</view>
        </view>
      </view>
    </u-popup>
    <!-- 提示隐藏弹窗 -->
    <u-popup v-model="isHideTips" mode="center" width="654rpx" height="auto" border-radius="22">
      <view class="tl-up-card">
        <view class="tl-flex-row-center mb-64">
          <view class="tl-font-32-333-500">您报名此活动的信息将对外隐藏确定隐藏吗？</view>
        </view>
        <view class="tl-flex-row-bwt">
          <view class="tl-btn-yes u-b-b-subject-border-cancel" @tap="isHideTips = false">取消</view>
          <view class="tl-btn-no u-b-b-subject-border" @tap="isHideChange">确定</view>
        </view>
      </view>
    </u-popup>
    <full-loading :show="fullLoading" bg-color="#fff"></full-loading>
	</view>
</template>

<script>
	import fullLoading from "../../components/full-loading/full-loading.vue";
  import wx from "../../pagesMy/bind/wx";
  import utilsWeek from "../../util/utilsweek"
  import assetIcon from "../../components/web3/asset-icon.vue";
  import typeTips from "../../components/activitv/type-tips.vue";

  export default {
    components: {typeTips, assetIcon, fullLoading},
		data() {
			return {
        fullLoading:true,
				activityInfo: {},
				startTime: '',
				endTime: '',
				userInfo: {},
				faceUrl: '',
				name: '',
				items: [],
				current: 0,
				agreementChecked: false,
				applyCount: 1, //报名人数
        curApplyCount: 1,//当前的报名人数
				applyName: '', //报名昵称
				isUseCoin: false,
				isWechatPay: true, //支付方式
				isPayPop: false,
				border: false,
        deduction:0,//可抵扣/金额
        deductionSub:0,//玩籽可抵扣金额合计
        deductionCurrencySub:0,//可抵扣玩籽合计
        isDeduction:false,//是否可抵扣玩籽
        // amount:0,//价格合计
        activity_id:'',//活动id
        grouping:'',//选择门票数据
        vest_id:'',//分享人id
        submitQuestionnaireId:0,//问卷提交的id
        collection:[],//
        cancelList:['随时取消','前6小时取消','前12小时取消','前24小时取消','前48小时取消',"不可退款"],
        canceNumlList:[0,6,12,24,48],
        questionnaire:'',//是否有问卷
        payState:false,//支付状态
        assetType:2,//1藏品2勋章
        isHide:false,//是否隐藏活动
        controlStatus: false,
        isHideTips:false,//是否隐藏弹窗
        memberDiscount:{},//会员折扣信息
        isQuestionnaireTips:false,//问卷提醒
			}
		},
		computed:{
			// amount(){
			// 	let curPrice = this.applyCount * this.grouping.collect_fee;//总价
      //   let curDiscount = Number((curPrice - (this.memberDiscount.discount?this.memberDiscount.discount:0) * curPrice));//折扣之后价格
      //   let curCollectFee = this.memberDiscount.discount?curDiscount:curPrice;
      //   this.$log("222",curCollectFee)
      //   return (curCollectFee - Number((this.isDeduction?this.deductionSub:0).toString().match(/^\d+(?:\.\d{0,2})?/))).toFixed(2);
			// },
      amount(){
        let curPrice = this.applyCount * this.grouping.collect_fee;//总价
        let curDiscount = Number((this.memberDiscount.discount?this.memberDiscount.discount:0) * curPrice);//折扣之后价格
        let curCollectFee = this.memberDiscount.discount?curDiscount:curPrice;
        this.$log("222",curDiscount,curCollectFee)
        return (curCollectFee - Number((this.isDeduction?this.deductionSub:0).toString().match(/^\d+(?:\.\d{0,2})?/))).toFixed(2);
      },
      surplusQuota() {
        return (this.grouping.attendance - this.grouping.registration_population) - this.curApplyCount;
      },
      allAmount() {
        return (this.grouping.collect_fee * this.applyCount).toFixed(2);
      },
      // allDeduction() {
      //   let curPrice = this.applyCount * this.grouping.collect_fee;//总价
      //   let curDiscount = this.memberDiscount.discount?this.memberDiscount.discount * curPrice:0;//折扣价格
      //
      //   return (curDiscount + Number((this.isDeduction?this.deductionSub:0).toString().match(/^\d+(?:\.\d{0,2})?/))).toFixed(2);
      // }
      allDeduction() {
        let curPrice = this.applyCount * this.grouping.collect_fee;//总价
        let curDiscount = this.memberDiscount.discount?curPrice - (this.memberDiscount.discount * curPrice):0;//折扣价格

        return (curDiscount + Number((this.isDeduction?this.deductionSub:0).toString().match(/^\d+(?:\.\d{0,2})?/))).toFixed(2);
      }
		},
    watch: {
      isHide(val) {
        // 等于false，意味着用户手动关闭了switch
        if (val == true) {
          if(this.controlStatus == true) {
            this.controlStatus = false;
            return ;
          }
          // 重新打开switch，并让它处于加载中的状态
          this.isHide = false;
          // 模拟向后端发起请求
          this.isHideTips = true;
        }
      }
    },
		onLoad(options) {
			let self = this;
      let tempData = JSON.parse(decodeURIComponent(options.data));
      this.$log("onLoad",tempData)
			self.activity_id = tempData.id;
      self.grouping = tempData.grouping;//价格分组id
      this.$log("grouping",self.grouping)

      self.deduction = (self.grouping.collect_wz_money * 0.1).toFixed(2);
      self.deductionSub = self.deduction;
      if(tempData.vest_id) {
        self.vest_id = tempData.vest_id;
        this.$log("活动支付的分享人id",self.vest_id)
      }
			/*self.userInfo = JSON.parse(uni.getStorageSync('sysUser'));
			this.$log('用户信息', self.userInfo)*/
			self.getActivityInfo();
      self.getMemberDiscount();
			// self.getBaiduFaceUrl();
		},
    destroyed() {
      this.$log("destroyed页面卸载")
      uni.setStorageSync("curConLists",'')
    },
    onUnload() {
      this.$log("onUnload页面卸载")
    },
		onShow() {
			let self = this;
			self.getUserMsg(); //如果用户进行了实名认证，需要刷新用户的信息
      // self.getPrice();
			if (this.agreementChecked) {
				self.getMJ() //处理用户操作了马甲刷新列表的操作
			}

      this.$log("页面收集信息",this.collection)
		},

		methods: {
      cancelQuestionnaire() {
        this.isQuestionnaireTips = false;
        uni.navigateBack({
          delta: 1
        });
        uni.$emit('refreshDetails');
      },
      goQuestionnaire() {
        this.isQuestionnaireTips = false;
        this.goCollection();
      },
      getMemberDiscount() {
        this.$http.get("getMemberDiscount",{act_id:this.activity_id}).then((res)=>{
          this.memberDiscount = res.data.data || {};
          this.$log('getMemberDiscount000',this.memberDiscount)
          if(!this.memberDiscount.discount) return;
          let max = this.grouping.collect_fee * this.memberDiscount.discount;//最高折扣
          // let max = this.grouping.collect_fee - (this.grouping.collect_fee * this.memberDiscount.discount);//最高折扣
          if(Number(this.grouping.collect_wz_money / 10) >= max) {
            this.grouping.collect_wz_money = (max * 10).toFixed(2);
            this.deduction = (this.grouping.collect_wz_money * 0.1).toFixed(2);
            this.$log('getMemberDiscount111', this.grouping.collect_wz_money,this.deduction)
          }
        })
      },
      isHideChange() {
        this.controlStatus = true;
        // 后端允许用户关闭switch，设置checked为false，结束loading状态
        this.isHideTips= false;
        this.isHide = true;
        this.$log("isHideChange",this.isHide)
      },
      getQuestionnaire(id) {
        this.$log("检查问卷参数",id)
        let params = {
          act_id:id,
        }
        this.$http.get('getQuestionnaire', params).then((res) => {
          let data = res.data.data;
          this.questionnaire = data.user_info_table;
          this.$log("检查问卷",res)
        })
      },
			// 获取活动详情
			getActivityInfo() {
				let self = this;
				let params = {
					id: self.activity_id
				}
				self.$http.get('getActivityInfo', params).then((res) => {
          self.fullLoading = false;
          let data = res.data.data;
          //获取问卷
          self.getQuestionnaire(data.id);

          this.$log("支付活动详情", data)
          self.activityInfo = data;
          self.activityInfo.picture_url = data.picture_url[0] === '[' ? JSON.parse(data.picture_url)[0].path:[];
          // self.activityInfo.distance = (parseInt(data.distance) / 1000).toFixed(1),//距离
          //剩余报名人数
          // self.activityInfo.surplus_apply_quantity = data.attendance - data.registration_population;
          [self.startTime,self.endTime] = utilsWeek.timeFormatting(data.start_time,data.end_time);
          let phone = data.issuer_phone;
          self.customerPhone = phone.substr(0, 3) + "****" + phone.substring(7);

          //TODO:扣除金额和玩籽汇率暂无
          // self.activityInfo.apply_seed = data.collect_wz_money;
          // self.deduction = (data.collect_wz_money * 0.1).toFixed(1);
          // 累计的可抵扣金额
          // self.deductionSub = self.deduction;
          // 累计的玩籽数量
          // self.deductionCurrencySub = data.collect_wz_money;

          self.onApplyCountChange();//计算折扣
				})
			},
			onApplyCountChange(e) {
        this.curApplyCount = e?e.value:this.curApplyCount;
        this.countNotSufficientFunds((value)=>{
          // 累计的可抵扣金额
          this.deductionSub = (this.deduction * this.curApplyCount).toFixed(2);
          // 累计的玩籽数量
          this.deductionCurrencySub = this.grouping.collect_wz_money * this.curApplyCount;
          this.$log("够抵扣", this.curApplyCount,this.deductionSub,this.deductionCurrencySub)
        });

        // this.getPrice()
			},
			onUseCoin(e) {
        if(e.value) {
          this.isDeduction = true;
          // this.getPrice();
        }else {
          this.isDeduction = false;
          // this.getPrice();
        }
        this.onApplyCountChange();
			},
      // 请求服务器玩籽抵扣价格
      getPrice () {
        let params = {
          activity_id:this.activity_id,
          amount:this.curApplyCount,
          isSeed:this.isDeduction?'1':'0'
        }
        this.$http.post("getPrice",params).then((res, error)=>{
          if(res.data.code == 200) {
            this.$log("玩籽抵扣请求成功",res.data.data.price)
            this.amount = res.data.data.price.toFixed(1) || 0;
          }else {
            this.$common.toast(res.data.msg,2);
          }
        })
      },
      // 计算余额是否足够抵扣
      countNotSufficientFunds(fun) {
        // 先计算是否足够抵扣
        this.deductionCurrencySub = this.grouping.collect_wz_money * this.curApplyCount;
        this.$log("是否够抵扣",this.deductionCurrencySub,this.userInfo.wz)
        if(this.deductionCurrencySub >= this.userInfo.wz) {
          this.deductionSub = (this.userInfo.wz * 0.1).toFixed(2);
          this.deductionCurrencySub = this.userInfo.wz
          this.$log("不够抵扣",this.curApplyCount,this.deductionSub,this.deductionCurrencySub)
        }else {
          fun();
        }
      },
			shareToggle() {
        // 切换微信支付宝
				// this.isPayPop = true;
			},
			onChangePay(type) {
				if (type) {
					this.isWechatPay = false;
				} else {
					this.isWechatPay = true;
				}
				this.isPayPop = false;
			},
			//分流处理，如果报名需要缴费，那么走生成订单流程，无需费用的走原来的报名流程
			saveIsOrder() {
        if(this.payState) {
          uni.showToast({
            title: '正在支付中...',
            duration: 2000,
            icon: "none"
          });
          return
        }
        uni.showLoading({
          title: '支付中...',
          mask: true
        });
				let money = this.grouping.collect_fee;
        this.payState = true;
        this.saveOrderData();
			},
			//收费支付保存数据
			saveOrderData() {
				let self = this;
				// let params = {
				// 	activity_id: self.activity_id,
				// 	pay_type: 3, //3-微信jsapi
				// 	platform: 2, //平台：1-app，2-小程序
				// 	weixin_openid: self.userInfo.weixin_openid,
        //   sharer_id:self.vest_id || 0,
        //   amount:self.curApplyCount,
        //   isSeed:self.isDeduction?'1':'0'
				// }
        let params = {
          detail_id: Number(self.activity_id),
          use_wz:self.isDeduction?true:false,
          user_count:self.curApplyCount,
          share_id:self.vest_id || 0,
          // is_hide:self.isHide,//是否隐藏我的信息
          ticker_type_id:self.grouping.ticker_id,//分组id
          discount:self.memberDiscount.discount || 1,//折扣
        }

        // let params = {
        //   detail_id: 752,
        //   use_wz:true,
        //   user_count:1,
        //   share_id:0,
        //   ticker_type_id:20,//分组id
        //   discount:1,//折扣
        // }
				//如果是昵称马甲报名，多加入一个参数
				// if (self.applyName) {
				// 	params['apply_name'] = self.applyName; //报名昵称
				// }
        self.$log("收费活动支付",params)
        self.$http.post('saveOrder', params,true,{callBack:(res)=>{
            self.$log("回调",res)
            uni.hideLoading();
            if(res.data.code === 200)return;
            self.$common.toast(res.data.message,3);
          }}).then((res) => {
          self.payState = false;
          self.$log('收费活动生成订单数据', res)
          // return;
          let obj = res.data.data;
          // let obj = res.data.data.wx_params;
          if(obj.isPaid) {
            self.$common.toast('报名成功');
            setTimeout(() => {
              // 判断问卷是否填写
              if(self.questionnaire) {
                self.isQuestionnaireTips = true;
                return;
              }
              uni.navigateBack({
                delta: 1
              });
              uni.$emit('refreshDetails');
            }, 800)
            return
          };
          self.requestPayment(obj);
				})
			},
      //提交问卷
      submitQuestionnaire() {
        let self = this;
        let params = {
          user_info_table:JSON.stringify(self.collection),
          act_id:self.activity_id,
          id:self.submitQuestionnaireId
        }
        this.$log("提交问卷的参数",params)
        this.$http.post('submitQuestionnaire',params).then((res)=>{
          if(res.data.code == 200) {
            this.$log("问卷提交成功",res.data)
          }else {
            this.$common.toast(res.data.message,2)
          }
        })
      },
			requestPayment(obj) {
				let self = this;
				let wx_params = obj;
        self.$log('支付参数======',wx_params)

        // nonceStr: "xOrpRyjKUCrtUigrIXH3Fu9g46Kw9kKh"
        // package: "prepay_id=wx12163248971041f71819933151d29c0000"
        // paySign: "Zt7t8pO6O3wPLz73387475syBFQmt1J/Jnss9B4MKQIHluYMO2hDGtQ2Quo/zOn9qCADi063wPLqsUqgDo/FnqTwzKU8wg8bFy0N5R5ML5kH3jkOeGtkSNpWZWWRwRh2CSf1Y0E7+Cpt9Uv5c24f+o+Bo39q/z4x2hNVxjlb8+CfcDce5idEO0ZVTNm0I2A4okn14UBd08WxbH+Qe4neL3cTe/QnJnhCahkNoI+nau8rrztNtlNYds6aKWiMV2alrMy8U97lOdsK2d2EhH8pawlJSdTTyKaTT4pPsQNXDb3v+e+iel6Us0qwQA+6sqwvLuy/l7pymJn76ybfWGy2/A=="
        // signType: "RSA"
        // timeStamp: "1710232368"
				uni.requestPayment({
					provider: 'wxpay',
					timeStamp: wx_params.timeStamp,//时间戳
					signType: wx_params.signType,// 签名算法，应与后台下单时的值一致
					package: wx_params.package,//统一下单接口返回的prepay_id参数值
					nonceStr: wx_params.nonceStr,//随机字符串
					paySign: wx_params.paySign,// 签名，具体见微信支付文档
					success: function(res) {
						self.$common.toast('支付成功')
            self.$log("支付成功",res)
						setTimeout(() => {
							// uni.redirectTo({
							// 	url: '/pages/details/details?id=' + self.activity_id
							// })
              if(self.questionnaire) {
                self.isQuestionnaireTips = true;
                return;
              }
              uni.navigateBack({
                delta: 1
              });
              uni.$emit('refreshDetails');
						}, 800)
            self.$log('success:' + JSON.stringify(res));
					},
					fail: function(err) {
            self.$log('fail:' + JSON.stringify(err));
					}
				});
			},
			//获取用户信息
			getUserMsg() {
				this.$http.get('getSelfInfo', '').then((res) => {
					if (res.data.code == 200) {
						let user = res.data.data;
						this.userInfo = user;
						// if (user.identity_auth_status) {
						// 	//this.name = user.identity_authentication.name.replace(/(?<=[\u4e00-\u9fa5]).*(?=[\u4e00-\u9fa5])/, "*")
            //
            //
						// 	let strName = user.identity_authentication.name;
						// 	if (strName.length == 2) {
						// 		this.name = strName.substring(0, 1) + '*' //截取name 字符串截取第一个字符，
						// 	} else if (strName.length == 3) {
						// 		this.name = strName.substring(0, 1) + "*" + strName.substring(2, 3) //截取第一个和第三个字符
						// 	} else if (strName.length > 3) {
						// 		this.name = strName.substring(0, 1) + "*" + '*' + strName.substring(3, strName
						// 			.length) //截取第一个和大于第4个字符
						// 	}
						// }
						uni.setStorageSync('sysUser', JSON.stringify(user));
						this.$log('res==============', res)
					} else {
						this.$common.toast(res.data.message,2)
					}
				})
			},
			//获取百度实名认证的url
			getBaiduFaceUrl() {
				let self = this;
				self.$http.post('getBaiduFaceUrl', {}).then((res, error) => {
					if (res.data.code == 200) {
						self.faceUrl = res.data.data.url;
						this.$log('获取百度实名认证的url', res.data.data.url)
					} else {
						self.$common.toast(res.data.msg)
					}
				})
			},
			/*
			 * 获取马甲列表，
			 * 如果存在就展示，如果不存在就展示添加按钮，添加的时候调接口添加一个ma甲
			 * */
			getMJ() {
				this.$http.post('getVestList', '').then((res, error) => {
					if (res.data.code == 200) {
						let list = res.data.data;
						this.items = [...list]
						this.$log(this.items)
					} else {
						this.$common.toast(res.data.message,2)
					}
				})
			},
			/*需要弹出再次确认框的*/
			goFace2() {
				let self = this;
				uni.showModal({
					content: '该活动需要实名认证',
					showCancel: false,
					showConfirm: false,
					success: function(res) {
						if (res.confirm) {
							/*let str = encodeURIComponent(self.faceUrl); //后端接口返回的一个认证地址
			      uni.navigateTo({
			          url: '/pagesMy/realName/face?url='+str
			      })*/
							uni.navigateTo({
								url: '/pagesMy/realName/realName'
							})
						} else if (res.cancel) {
							this.$log('用户点击取消');
						}
					}
				});
			},
      // 信息收集
      goCollection() {
        this.$log("信息收集跳转",this.collection,this.collection.length)
        uni.navigateTo({
          url:'/pages/details/collection?id=' + this.activity_id
        })

        // if(this.collection && this.collection.length > 0) {
        //   let tempData = JSON.stringify(this.collection)
        //   uni.navigateTo({
        //     url:'/pages/details/collection?id=' + this.activity_id + '&tempData=' + encodeURIComponent(tempData)
        //   })
        // }else {
        //   uni.navigateTo({
        //     url:'/pages/details/collection?id=' + this.activity_id
        //   })
        // }

      },

		}
	}
</script>

<style lang="scss" scoped>
	.tl-img-272 {
		width: 256rpx;
    height: 180.3rpx;
    border-radius: 20rpx;
	}

	.tl-h2-32 {
		font-size: 32rpx;
		font-family: Source Han Sans SC-Medium, Source Han Sans SC;
		font-weight: 500;
		color: #343434;
	}

	.tl-cut-off-rule {
		width: 100%;
		border: 2rpx dashed #EBEBEB;
		margin: 32rpx 0;
	}

	.tl-font-20-gray {
    height: auto;
		margin-left: 14rpx;
		font-size: 24rpx;
		font-family: Source Han Sans SC-Normal, Source Han Sans SC;
		font-weight: 400;
		color: #767676;
		//line-height: 1;
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

	.tl-font-32-333333 {
		font-size: 32rpx;
		font-family: Source Han Sans SC-Medium, Source Han Sans SC;
		font-weight: 500;
		color: #333333;
		margin-right: 20rpx;
	}
  .tl-tipe-red {
    color: #E04646;
  }

	.tl-font-24-999999 {
		font-size: 24rpx;
		font-family: Source Han Sans SC-Normal, Source Han Sans SC;
		font-weight: 400;
		color: #999999;
	}

	.tl-row-ultimate {
		margin-left: auto;

		::v-deep .u-checkbox__label {
			margin: 0 !important;
		}
    ::v-deep .u-icon__icon {
      font-size: 28rpx !important;
      //color: #FF770F !important;
    }
	}

	.tl-pay-item {
		width: 125rpx;
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;

		>image {
			width: 28rpx;
			height: 28rpx;
		}

		>text {
			font-size: 28rpx;
			font-family: Source Han Sans SC-Medium, Source Han Sans SC;
			font-weight: 500;
			color: #333333;
		}
	}
  .tl-pay-item2 {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-left: auto;
  }
	.tl-explain {
		width: 100%;
		height: auto;
		margin-top: 24rpx;
		padding: 32rpx;
		background: #FFFFFF;
		border-radius: 20rpx;
	}

	.tl-font-28-66 {
		font-size: 28rpx;
		font-family: Source Han Sans SC-Normal, Source Han Sans SC;
		font-weight: 400;
		color: #939393;
		list-style: none;
    padding: 0 !important;
	}

	.tl-font-28-33 {
		font-size: 28rpx;
		font-family: Source Han Sans SC-Normal, Source Han Sans SC;
		font-weight: 400;
		color: #333333;
    line-height: 80rpx;
	}

	.tl-font-32-ff {
		font-size: 32rpx;
		font-family: Source Han Sans SC-Bold, Source Han Sans SC;
		font-weight: bold;
	}

	// 弹窗公共样式
	.tl-payPop {
		width: 100%;
		height: 100%;
		padding: 50rpx;
		display: flex;
		flex-direction: column;
		justify-content: center;

		.tl-pay-pop-item {
			width: 100%;
			height: 80rpx;
			display: flex;
			justify-content: flex-start;
			align-items: center;
			margin: 20rpx 0;
			padding: 0 150rpx;
			background-color: #F8FEFF;

			>image {
				width: 60rpx;
				height: 60rpx;
				margin-left: 65rpx;
			}

			>text {
				margin: auto;
				font-size: 35rpx;
				font-family: Source Han Sans SC-Medium, Source Han Sans SC;
				font-weight: 500;
				color: #333333;
			}
		}
	}
  .tl-margin-8 {
    margin-left: 8rpx;
  }

  .tl-img-32 {
    width: 32rpx;
    height: 32rpx;
  }
  .tl-font-32-00-500 {
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: bold;
    font-size: 32rpx;
    color: #000000;
    line-height: 28rpx;
    text-align: right;
  }
  .tl-font-24-33 {
    font-size: 24rpx;
    font-family: Source Han Sans SC-Normal, Source Han Sans SC;
    font-weight: 400;
    color: #333333;
  }

	.page-bg {
		width: 100vw;
		//height: auto;
    //height: 100vh;
		background-size: 750rpx auto;
		background-color: #F7F7F7;

		.main-container {
			width: 686rpx;
			margin: 0 auto;
			//padding-bottom: 180rpx;
			display: flex;
			flex-direction: column;

			.content-card {
				width: 100%;
				//height: 636rpx;
        height: auto;
				background: #FFFFFF;
				border-radius: 20rpx;
				padding: 32rpx;
        margin-top: 40rpx;
				.card-info {
					width: 100%;
					height: 180rpx;
					display: flex;
					align-items: center;

					.info-left {
						width: 256rpx;
            height: 180rpx;
					}

					.info-right {
            flex: 1;
						margin-left: 14rpx;

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
              .image-24-border {
                width: 28rpx;
                height: 28rpx;
                min-width: 28rpx;
                border-radius: 6rpx;
              }

						}

						.address-distance {
							height: 30rpx;
							display: flex;
							align-items: center;
							margin: 8rpx 0;

							.font-20-66 {
								font-size: 20rpx;
								font-family: Source Han Sans SC-Normal, Source Han Sans SC;
								font-weight: 400;
								color: #666666;
								margin-left: auto
							}
						}
					}
				}

				.card-tiem {
					width: 100%;
					height: 48rpx;
					margin-bottom: 28rpx;
					display: flex;
					align-items: center;

					.info-input {
						//width: 138rpx;
						height: 44rpx;
						border-radius: 8rpx;
            ::v-deep .u-number-input {
              margin: 0;
            }
					}
				}
        .card-tiem2 {
          width: 100%;
          height: 48rpx;
          display: flex;
          align-items: center;

          .info-input {
            //width: 138rpx;
            height: 44rpx;
            border-radius: 8rpx;
            ::v-deep .u-number-input {
              margin: 0;
            }
          }
        }

			}


		}
    .content-footer {
      position: fixed;
      bottom: 0;
      //left: 50%;
      //transform: translateX(-50%);
      //width: 686rpx;
      width: 100%;
      height: 176rpx;
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: #FFFFFF;
      padding: 0 32rpx;
      box-shadow: 0rpx 0rpx 8rpx 0rpx rgba(147,147,147,0.5);
      .footer-left {
        display: flex;
        align-items: center;
      }

      .footer-right {
        width: 240rpx;
        height: 71rpx;
        line-height: 71rpx;
        border-radius: 35.5rpx;
        border: none;
        margin: 0;
        font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
        font-weight: 600;
        font-size: 32rpx;
        color: #454545;
        text-align: center;
      }
    }
	}
</style>