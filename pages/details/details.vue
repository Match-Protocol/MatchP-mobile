<template>
  <view class="page-bg" :class="false?'tl-overflow-hidden':''">
    <u-navbar :custom-back="goBack" :border-bottom="false" back-icon-color="#242424" :back-icon-name="shareId?'home':'nav-back'">
      <view v-if="tribeInfo.tribe_name" class="slot-wrap" @tap="goTribe">
        <u-image class="tl-icon-50-r50" style="margin: 0 12rpx;" :src="tribeInfo && tribeInfo.tri_avatar"
                 :loading-icon="headImg" height="50" width="50" duration="450" :lazy-load="true"
                 border-radius="25">
        </u-image>
        <text class="tl-font-32-00 tl-text-overflow-1">{{ tribeInfo.tribe_name}}</text>
        <typeTips style="margin: 0 14rpx 0 8rpx" :name="tipsType[tribeInfo.label - 1]" :setSize="20"></typeTips>
        <view class="tl-mod">
          <view class="tl-to_right tl-center-h-v"></view>
        </view>
      </view>
    </u-navbar>
    <view class="main-container">
      <!--首页封面图-->
      <view class="banner-box" :style="{height: swiperHeight}" v-if="bannerList.length > 0">
        <swiper class="swiper swiper-box tl-img" :autoplay="false" :style="{height: swiperHeight}"
                indicator-color="rgb(0,0,0,0.3)"
                indicator-active-color="#000000" @change="changeSwiper" @animationfinish="animationfinish">
          <swiper-item v-for="(item, index) in bannerList" :key="index" @tap="onClickVideo">
            <image class="tl-img-658" v-if="item.type === 'image'" :id="'content-wrap' + index" mode="widthFix"
                   :src="item.url" @load="imageLoad($event,index)"/>
<!--            <u-image v-if="item.type === 'image'" :id="'content-wrap' + index" class="tl-img-658"-->
<!--                     :src="item.url" :loading-icon="loadingImg" border-radius="20"-->
<!--                     :lazy-load="true">-->
<!--              <u-loading slot="loading"></u-loading>-->
<!--            </u-image>-->
            <video class="tl-video" v-else :src="item.url" :id="'content-wrap' + index"
                   :show-center-play-btn="false" @loadedmetadata="videometa($event,0)"
                   :style="{height:videoHeight,width:videoWidth}"
                   :controls="false" :muted="isMuted" :direction="0"></video>
          </swiper-item>
        </swiper>
        <!-- 音频控制 -->
        <block v-if="bannerList[currentIndex].type === 'video'">
          <image v-if="isMuted" @tap="isMuted = false" class="audio-control" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/close_audio.png"/>
          <image v-else @tap="isMuted = true" class="audio-control" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/open_audio.png"/>
        </block>
        <!-- 自定义指示点的容器 -->
        <view class="custom-dots">
          <view v-for="(item, index) in bannerList" :key="index" class="dot" :class="{ active: index === currentIndex }"></view>
        </view>
        <!-- 价格显示 -->
        <view class="tl-button2 tl-flex-row-start">
          <text v-if="activityInfo.charge_type === 2" class="tl-font-24-b">押金可退</text>
          <image class="tl-img-35" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/wanzi.png"/>
          <button class="u-b-b-subject-border tl-flex-row-center2 cur-button" >
            <text class="tl-font-28">68</text>
            <text class="tl-font-24 tl-font-20">元/人</text>
          </button>
        </view>
      </view>
      <!-- 内容区域 -->
      <view class="content-info">
        <h3 class="tl-font-h3 mb-24">{{ activityInfo.theme }}</h3>
        <view class="tl-flex-row-start">
          <image class="tl-icon-32"
                 src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/time_n.png"></image>
          <span class="tl-font-28-66">活动时间:{{ startTime }}-{{ endTime }}</span>
        </view>
        <view class="tl-flex-row-bwt">
          <view class="tl-address">
            <view class="tl-img-box">
              <image class="tl-icon-32-l"
                     src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/location_n.png"></image>
            </view>
            <text v-if="activityInfo.address" class="tl-font-28-66 tl-address-line">活动地址:{{ activityInfo.address }}</text>
            <text v-else class="tl-font-28-66">线上活动</text>
          </view>
          <view class="tl-line"></view>
          <view class="tl-navigation">
            <image class="tl-img-48" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/navigation.png"/>
            去这里
          </view>
        </view>
      </view>

      <!-- 主理人 -->
      <view class="info-owner mb-20" @tap.stop="goSponsor(ownerInfo.id)">
        <headItem :headImg="ownerInfo.avatar || ''" :sexType="ownerInfo.gender || 0" :setSize="100"
                  :setBorderSize="6" :setBorderColor="'#1ACDE8'" style="margin-right: 22rpx">
        </headItem>

        <view class="owner-text">
          <view class="tl-flex-row-start mb-8">
            <view style="max-width: 70%" class="tl-font-28-00 tl-text-overflow-1">{{ ownerInfo.name || ' ' }}</view>
            <view class="tl-tips-box">发布人</view>
          </view>
          <text class="tl-font-24-00 tl-text-overflow-1">{{ownerInfo.label}}</text>
        </view>
        <view class="owner-contact tl-flex-row-start" @tap.stop="isContactShow = true" style="margin-left: auto">
          <view class="owner-line"></view>
          <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/contact_n.png"/>
        </view>
      </view>

      <!--报名人数-->
      <view class="info-apply mb-20" v-if="user_list.apply_number > 0">
        <view class="info-apply-title" @tap="isApplyNumShow = true">
          <h4 class="tl-font-28-34">报名人数</h4>
          <text class="tl-font-28-1a">已报名{{user_list.apply_number}}/{{user_list.total_number}}人</text>
        </view>
        <view class="info-apply-list" v-if="user_list.list && user_list.list.length > 0">
          <block v-for="(item,index) in user_list.list" :key="index">
            <view v-if="index < 6" class="apply-list-item"
                  @tap="goSponsor(item.user_id)">
              <headItem :headImg="item.avatar || ''" :sexType="item.gender || 0" :setSize="88" :sexBottom="'0rpx'"
                        :setBorderSize="2" :setBorderColor="'#FFFFFF '"
                        :sexSize="28" class="mb-10">
              </headItem>
              <text class="tl-font-24-00 tl-text-overflow-1">{{item.nickname}}</text>
            </view>
          </block>
        </view>
      </view>
      <!-- 活动介绍 -->
      <view class="content-introduce">
        <view class="introduce-title">活动描述</view>
        <rich-text :nodes="richObj"></rich-text>
      </view>
      <!--详情页图片视频-->
      <view v-if="bannerList.length > 1" class="content-introduce">
        <view class="introduce-title">详情图片</view>
        <view class="introduce-content" v-for="(item,index) in bannerList" :key="index"
              @tap="previewMediaInfo(index)">
          <u-image v-if="item.type === 'image' && index != 0" mode="widthFix"
                   :src="item.url" :loading-icon="loadingImg"
                   :lazy-load="true">
            <u-loading slot="loading"></u-loading>
          </u-image>
          <view class="tl-info-box" v-else-if="item.type === 'video'">
            <image v-show="videoHeight" class="tl-info-image" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/play_icon.png"/>
            <video class="tl-info-video" :src="item.url" object-fit="cover" :show-center-play-btn="false"
                   @loadedmetadata="videometa($event,1)" :style="{height:videoHeight1,width:videoWidth1}" :poster="item.poster"
                   :controls="false" :muted="isMuted" :direction="0"></video>
          </view>
        </view>
      </view>

      <view class="bottom-placeholder"></view>

      <view class="bottom-menu">
          <!-- 收藏和分享 -->
          <view class="btn-left">
            <view class="tl-btn-39">
              <button class="tl-menu-share" @tap="goLogin1"></button>
              <text class="tl-font-24-00">转发</text>
            </view>
            <view class="tl-btn-39" @tap="goLogin1">
              <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/collect_n.png"></image>
              <text class="tl-font-24-00">收藏</text>
            </view>
          </view>
          <!-- 加入社区和报名 -->
          <view class="btn-right">
            <!-- 判断是否登录 -->
            <view class="u-b-b-subject-gradual tl-btn-162 tl-btn-162-black">报名
              <text class="tl-font-28-45">(￥120/人)</text>
            </view>
          </view>
      </view>

    </view>
    <!--弹窗-->
    <u-popup v-model="isContactShow" mode="bottom" border-radius="22" :closeable="true" height="604">
      <view class="tl-contac-pop">
        <view class="head-item">
          <headItem :headImg="ownerInfo.avatar || ''" :sexType="ownerInfo.gender || 0" :setSize="100" :sexBottom="'-8rpx'"
                    :setBorderSize="6" :setBorderColor="'#1ACDE8'"
                    :sexSize="32" class="mb-10">
          </headItem>
          <text class="tl-font-24-00 mb-10">{{ownerInfo.name}}</text>
          <text class="tl-font-24-00">{{ownerInfo.label}}</text>
        </view>
        <block v-if="!isContactPay">
          <h3 class="tl-font-32-00-2 mb-16">联系该活动主理人</h3>
          <view class="tl-font-28-00 mb-65">您将消耗 <text class="u-c-subject2">20</text> 个玩籽来查看对方联系方式？</view>
          <view class="btn-pay" @tap="getContactInformation">立即支付</view>
        </block>
        <block v-else>
          <view v-if="false" class="btn-info">
            <text class="tl-font-32-00-2">微信号：</text>
            <text class="tl-font-32-00-2 u-c-subject2">chenghuiwan2024</text>
            <image class="tl-img-28" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/web3/copy.png"/>
            <text class="tl-font-28-00 u-c-subject2">复制</text>
          </view>

          <view v-else class="btn-info" @tap="callPhone">
            <text class="tl-font-32-00-2">手机号：</text>
            <text class="tl-font-32-00-2 u-c-subject2">{{activityInfo.issuer_phone}}</text>
            <image class="tl-img-44" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/call_phone_n.png"/>
            <text class="tl-font-28-00 u-c-subject2">拨打</text>
          </view>
        </block>
      </view>
    </u-popup>

    <!--报名人数弹窗-->
    <titlePop :shortTerm="isApplyNumShow" :setHeight="setShareTicketPOp()"
              :curText="'已报名' + activityInfo.registration_population + '/' + activityInfo.attendance + '人'"
              @changePop="applyNumChangePop">

      <view class="tl-apply-pop">
        <view class="tl-apply-num-box" v-for="(item,index) in ticketTypeList" :key="index">
          <text class="tl-font-28-93">{{item.type_name}} · {{item.registration_population}}/{{item.attendance}}人报名</text>
          <view class="tl-apply-num-item" v-for="(crewItem,crewIndex) in item.user_list" :key="crewIndex">
            <headItem :headImg="crewItem.avatar || ''" :sexType="crewItem.gender || 0" :setSize="88" :sexBottom="'-8rpx'"
                      :setBorderSize="6" :setBorderColor="'#FFFFFF'"
                      :sexSize="32" style="margin-right: 32rpx">
            </headItem>
            <view class="tl-flex-column-start2">
              <text class="tl-font-32-34 mb-8">{{crewItem.nickname}}</text>
              <text class="tl-font-24-93">{{crewItem.my_label?crewItem.my_label:'TA很懒，什么也没留下...'}}</text>
            </view>
          </view>
        </view>
      </view>
    </titlePop>
  </view>
</template>

<script>
import typeTips from "../../components/activitv/type-tips.vue";
import headItem from "../../components/common/head-item.vue";
import titlePop from "../../components/common/title-pop.vue";

export default {
  components: {
    typeTips,
    headItem,
    titlePop
  },
  data() {
    return {
      headImg: 'https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/head.jpg',
      loadingImg: 'https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/img_loading2.jpg',
      isLogin: false, // 是否已登录 默认未登录
      activity_id: "",
      bannerList: [
        {poster:'',type:"image",url:"https://goin.obs.cn-north-4.myhuaweicloud.com/wechat/1736646863418288138686010200.jpg"},
        {poster:'',type:"image",url:"https://goin.obs.cn-north-4.myhuaweicloud.com/wechat/1736646886346630566554658303.jpg"},
        {poster:'',type:"image",url:"https://goin.obs.cn-north-4.myhuaweicloud.com/wechat/1736647153102071260771273587.jpg"},
        {poster:'',type:"image",url:"https://goin.obs.cn-north-4.myhuaweicloud.com/wechat/1736647178419115488324216276.jpg"}
      ],
      tribeInfo: {
        tribe_id:89,
        tribe_name:"拾菌小组",
        tri_avatar:"https://goin.obs.cn-north-4.myhuaweicloud.com/wechat/1736313556322113523270834174.jpg",
        label:4,
      },// 社区详情
      activityInfo: {
        act_type: 9,
        activity_introduction: "<p>所有免费8.8元</p>",
        addr_coordinate_original: "103.98657 30.67735",
        address: "清波社区党群服务中心",
        address_type: 2,
        attendance: 145,
        audit_status: 7,
        charge_type: 1,
        collect_total: 0,
        distance: 0,
        end_time: 1736686800,
        id: 46,
        is_apply: false,
        is_collect: false,
        is_hide: false,
        is_promotion: false,
        is_push: 0,
        issuer_avatar: "https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/head/head03.jpg",
        issuer_gender: 1,
        issuer_id: 12,
        issuer_label: "中二少年一枚",
        issuer_name: "白醋",
        issuer_phone: "18708130825",
        label: 4,
        picture_url: [],
        promotion_ratio: 0,
        refund_model_id: 4,
        registration_population: 0,
        role: 0,
        start_time: 1736650800,
        status: 7,
        theme: "手工针织",
        ticker_type:[],
        tri_avatar: "https://goin.obs.cn-north-4.myhuaweicloud.com/wechat/1736313556322113523270834174.jpg",
        tribe_id: 89,
        tribe_name: "拾菌小组",
        user_avatars: "[]"
      },
      startTime: "",
      endTime: "",
      user_list: [], //参会人员
      setting: 0,//边距设置
      fullLoading: true, //展示加载动画
      ownerInfo: {
        name:"白醋",
        avatar:"https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/head/head03.jpg",
        phone:'17599994800',
        label:'中二少年一枚',
        id:0,
        gender:1
      },
      deliveryFlag: false,
      posterData: {},
      richObj: '', //富文本
      shareId: '',
      isApplyEnd: false,//是否超过报名时间
      isStart: false,//活动是否开始
      richObjStyle: {
        p:'padding:0 32rpx',
        img:'max-width:100%;height:auto;border-radius:12rpx'
      },
      is_collect: false,//是否收藏
      currentIndex:0,
      swiperHeight:'488rpx',
      isContactShow:false,
      isContactPay:false,//是否已支付获取联系方式
      isApplyShow:false,//报名弹窗
      curApplyIndex:0,
      isApplyNumShow:false,//报名人弹窗
      playId:'',
      isMuted:true,
      ticketTypeList:[],//门票类型列表
      floorPriceData:{},//门票最低价
      videoWidth:0,
      videoHeight:0,
      videoWidth1:0,
      videoHeight1:0,
      personalId:'',
      isAlreadyCreated:false,//是否已经创建推广订单
      isQuestionnaireTips:false,
      tipsType:['俱乐部','场地','社群','DAO','社区','小区','学校','村落','协会','单位'],
    };
  },
  onLoad(options) {

  },
  onShow() {
  },
  created() {
  },
  mounted() {
  },
  methods: {
    setShareTicketPOp() {
      let num = 0;
      this.ticketTypeList.map((item,index)=>{
        num += item.registration_population
      })
      return num > 8? '70%':'auto';
    },
    imageLoad(e,index) {
      if(index === 0) {
        this.setSwiperHeight();
      }
    },
    getContactInformation() {//支付玩籽获取主理人联系方式

    },
    applyChangePop(value) {
      this.isApplyShow = value;
    },
    onApplyNotarize() {
      this.isApplyShow = false;
      console.log("onNotarize",this.ticketTypeList[this.curApplyIndex])
    },
    checkboxChange(e) {
      this.curApplyIndex = Number(e.detail.value);
    },
    applyNumChangePop(value) {
      this.isApplyNumShow = value;
    },
    changeSwiper(e) {
      this.currentIndex = e.detail.current;
      console.log("changeSwiper00000",this.currentIndex)
    },
    onClickVideo() {
      console.log("onClickVideo",this.playId);
      if(this.bannerList[this.currentIndex].type === 'video') {
        let videoContext = uni.createVideoContext(this.playId);
        videoContext.pause();
      }
      this.previewMedia();
    },
    previewMedia() {
      console.log("previewMedia")
      wx.previewMedia({
        current:this.currentIndex,
        sources:this.bannerList
      });
    },
    previewMediaInfo(index) {
      console.log("previewMediaInfo")
      wx.previewMedia({
        current:index,
        sources:this.bannerList
      });
    },
    videometa (e,type) {
      let that = this;
      //获取系统信息
      uni.getSystemInfo({
        success (res) {
          //视频的高
          let height = e.detail.height;
          //视频的宽
          let width = e.detail.width;
          //算出视频的比例
          let proportion = height / width;
          //res.windowWidth为手机屏幕的宽。
          let windowWidth = res.windowWidth;
          //算出当前宽度下高度的数值
          let resultHeight = proportion * windowWidth + 'px';
          let resultWidth = windowWidth + 'px';
          if(type === 1) {
            that.videoHeight1 = resultHeight;
            that.videoWidth1 = resultWidth;
          }else {
            that.videoHeight = resultHeight;
            that.videoWidth = resultWidth;
          }
        }
      })
    },
    animationfinish(e) {
      console.log("animationfinish",e,this.currentIndex)
      this.$nextTick(() => {
        this.setSwiperHeight();
        if(this.playId != '') {
          console.log("element1",this.playId);
          let videoContext = uni.createVideoContext(this.playId);
          videoContext.pause();
        }
        if(this.bannerList[this.currentIndex].type === 'image')return;
        this.playId = "content-wrap" + this.currentIndex;
        console.log("element2",this.playId)
        let videoContext = uni.createVideoContext(this.playId);
        videoContext.play();
      });
    },
    setSwiperHeight() {
      let element = "#content-wrap" + this.currentIndex;
      let query = uni.createSelectorQuery().in(this);
      query.select(element).boundingClientRect();
      query.exec((res) => {
        if (res && res[0]) {
          console.log("setSwiperHeight",res)
          this.swiperHeight = (res[0].height <= 0 ? 244 : res[0].height) + 'px';
        }else {
          this.swiperHeight = '488rpx'
        }
      });
    },
    getActivityInfo() {
      let self = this;
      let params = {
        id: self.activity_id,
      };
      console.log("获取活动信息", params)
      return;
      self.$http.get("getActivityInfo", params,false,{callBack:()=>{
          self.$nextTick(()=>{
            self.fullLoading = false;
          })
        }}).then(async (res) => {
        console.log("请求的回调", params, res)
        let data = res.data.data;
        // 活动详情
        self.bannerList = data.picture_url[0] === '[' ? self.setBannerList(JSON.parse(data.picture_url)):[];
        console.log("bannerList", self.bannerList)
        self.activityInfo = data;
        // self.activityInfo.surplus_apply_quantity = data.attendance - data.registration_population;
        self.activityInfo.is_apply = self.isLogin?await self.getIsApply(data.id):false;
        this.floorPriceData = self.activityInfo.ticker_type?self.activityInfo.ticker_type.reduce((pre,cur)=>pre.collect_fee<cur.collect_fee?pre:cur):{}
        console.log("最低价格列表", this.floorPriceData)
        console.log("获取是否报名", self.activityInfo.is_apply)
        // 活动状态
        self.activityInfo.status = data.audit_status;
        console.log("活动详情===", self.activityInfo);
        // self.endTime = self.timeStamp(data.end_time);
        console.log("活动时间", self.startTime,self.endTime);

        // self.richObj = self.formEditor(data["activity_introduction"]);
        self.richObj = "<p>所有免费8.8元</p>";

        // 主理人信息
        self.ownerInfo.name = data.issuer_name;
        self.ownerInfo.avatar = data.issuer_avatar;
        self.ownerInfo.id = data.issuer_id;
        self.ownerInfo.label = data.issuer_label;
        self.ownerInfo.phone = data.issuer_phone;
        self.ownerInfo.gender = data.issuer_gender;
        // 社区信息
        self.tribeInfo = {
          tribe_id:data.tribe_id,
          tribe_name:data.tribe_name,
          tri_avatar:data.tri_avatar,
          label:data.label,
        };

        self.makeMyCode(); //调用一次生成海报相关数据
        self.$nextTick(()=>{
          self.fullLoading = false;
        })
      });
    },
    formEditor(editorDetails) {
      // 活动详情富文本处理
      // console.log("活动详情富文本处理11",editorDetails)
      const reg = /style\s*?=\s*?(['"])[\s\S]*?\1/g;
      return editorDetails?editorDetails.replace(reg, "")
          .replace(
              /\<img/gi,
              '<img style="max-width:100%;height:auto"')
          .replace(
              /\<p/gi,
              '<p style="padding:0 32rpx "')
          .replace(
              /\<p style="padding:0 32rpx "><img/gi,
              '<p style="padding:22rpx 0"><img'
          ):editorDetails;
    },
    //获取是否报名
    getIsApply(id) {
      return new Promise((resolve, reject) => {
        let params = {
          act_id: id,
        }
        this.$http.get('getIsApply', params).then((res) => {
          if (res.data.code == 200) {
            resolve(res.data.data);
            console.log("获取是否报名11111", res)
          } else {
            this.$common.toast(res.data.message, 2)
          }
        })
      })
    },
    setBannerList(data) {
      let dataList = [];
      if(data.length <= 0)return;
      data.map((item)=>{
        let tempList = {
          url:item.path,
          type:item.type,
          poster:item.thumbPath || ""
        };
        dataList.push(tempList);
      });
      return dataList;
    },
    //收藏和取消收藏
    saveCollection(num) {

    },
    goBack() {
      uni.navigateBack();
    },
    // 未登录跳转
    goLogin1() {

    },
    //跳转到主办方的活动列表
    goSponsor(id) {

    },
    // 去活动社区
    goTribe() {
    },
    // 报名
    goRealName(groupingId) {

    },
    //拨打电话
    callPhone() {
      let self = this;
      uni.makePhoneCall({
        phoneNumber: self.activityInfo.issuer_phone + "",
      });
    },
  }

};
</script>

<style lang="scss" scoped>
/deep/ .u-image__image {
  display: block;
}
/*每个页面公共css */
.content {
  text-align: center;
  height: 100%;
}

.qrcode {
  position: relative;
  left: -100px;
}

.tl-font-h3 {
  font-size: 36rpx;
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: bold;
  color: #342A25;
  word-break: break-all;
  text-overflow: ellipsis;
  display: -webkit-box;
  /** 对象作为伸缩盒子模型显示 **/
  -webkit-box-orient: vertical;
  /** 设置或检索伸缩盒对象的子元素的排列方式 **/
  -webkit-line-clamp: 2;
  /** 显示的行数 **/
  overflow: hidden;
  /** 隐藏超出的内容 **/
}
.tl-img-box {
  position: flex;
  justify-content: center;
  align-items: center;
  height: 44rpx;
  width: 32rpx;
  margin-right: 16rpx;
}
.tl-icon-32-l {
  width: 32rpx;
  height: 32rpx;
  min-width: 32rpx;
}
.tl-icon-32 {
  width: 32rpx;
  height: 32rpx;
  min-width: 32rpx;
  margin-right: 16rpx;
}

.tl-font-28-66 {
  font-size: 28rpx;
  font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
  font-weight: 400;
  line-height: 44rpx;
  color: #6D6C6B;
}
.tl-font-28 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 600;
  font-size: 28rpx;
  line-height: 48rpx;
}
.tl-icon-50-r50 {
  width: 50rpx;
  height: 50rpx;
  border-radius: 50%;
}
.tl-font-28-00 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 600;
  font-size: 28rpx;
  color: rgba(0,0,0,0.8);
  line-height: 38rpx;
  text-align: center;
}
.tl-font-32-00 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 600;
  font-size: 32rpx;
  color: #000000;
  line-height: 32rpx;
}
.tl-font-32-00-2 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 600;
  font-size: 32rpx;
  color: rgba(0,0,0,0.8);
  line-height: 44rpx;
  text-align: center;
}

.tl-btn-39 {
  width: 44rpx;
  //height: 44rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  animation: rubberBand 0.5s ease forwards;
  > image {
    width: 44rpx;
    height: 44rpx;
    min-width: 44rpx;
    margin-bottom: 8rpx;
  }
}

.tl-menu-share {
  width: 44rpx;
  height: 44rpx;
  padding: 0;
  border-style: none;
  margin-bottom: 8rpx;
  background: url('https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/share_n.png') no-repeat center/cover;
}

.tl-btn-162 {
  width: 488rpx;
  height: 80rpx;
  text-align: center;
  line-height: 80rpx;
}

.tl-btn-162-black {
  //border-radius: 10rpx;
  font-size: 32rpx;
  font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
  font-weight: 400;
  color: #FEFEFE;
  //margin-left: 40rpx;
}

.tl-btn-162-white {
  border-radius: 10rpx;
  border: 3rpx solid #000000;
  font-size: 28rpx;
  font-family: Source Han Sans SC-Medium, Source Han Sans SC;
  font-weight: 500;
}
.tl-address {
  width: 65%;
  display: flex;
  align-items: flex-start;
}
.tl-address-line {
  word-break: break-all;
  text-overflow: ellipsis;
  display: -webkit-box;
  /** 对象作为伸缩盒子模型显示 **/
  -webkit-box-orient: vertical;
  /** 设置或检索伸缩盒对象的子元素的排列方式 **/
  -webkit-line-clamp: 2;
  /** 显示的行数 **/
  overflow: hidden;
  /** 隐藏超出的内容 **/
}
.tl-font-24 {
  font-size: 24rpx;
  font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
  font-weight: 400;
  line-height: 24rpx;
}
.tl-text-overflow-1 {
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
.tl-mod {
  display: flex;
  width: 20rpx;
  height: 20rpx;
}
.tl-center-h-v {
  margin: auto;
}
/*空心右箭头*/
.tl-to_right {
  width: 20rpx;
  height: 20rpx;
  transform: rotate(45deg);
  border-right: 4rpx solid #C4C4C4;
  border-top: 4rpx solid #C4C4C4;
}
.tl-video {
  width: 100%;
  border-radius: 20rpx;
  overflow: hidden;
}
.audio-control {
  width: 48rpx;
  height: 48rpx;
  min-width: 48rpx;
  position: absolute;
  bottom: 70rpx; /* 根据需要调整位置 */
  left: 52rpx;
}
/* 自定义指示点样式 */
.custom-dots {
  position: absolute;
  bottom: 20rpx; /* 根据需要调整位置 */
  left: 52rpx;
}

.dot {
  width: 15rpx;
  height: 15rpx;
  border-radius: 50%;
  background-color: rgba(0,0,0, 0.3);
  margin: 0 5rpx;
  display: inline-block;
}

.dot.active {
  background-color: #000000;
}
.tl-button2 {
  position: absolute;
  right: 44rpx;
  bottom:12rpx;
  width:auto;
  height: 48rpx;
  line-height: 48rpx;
  //background: rgba(69,69,69, 0.3);
  background: linear-gradient( 87deg, #111E37 0%, rgba(35,52,87,0.3) 63%, rgba(247,247,247,0) 100%);
  border-radius: 42rpx;
  >button {
    height: 100%;
    background: #454545;
    border-radius: 42rpx;
    padding: 0 22rpx;
  }
  .cur-button {
    padding: 0 30rpx !important;
  }
}
.tl-img-35 {
  width: 32rpx;
  height: 32rpx;
  min-width: 32rpx;
  margin: 0 8rpx 0 12rpx;
}
.tl-padding-38 {
  padding: 0 30rpx;
}
.tl-font-20 {
  font-size: 20rpx !important;
}
.tl-line {
  width: 0;
  height: 68rpx;
  border: 2rpx solid rgba(0,0,0, 0.05);
}
.tl-font-24-b {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: bold;
  font-size: 24rpx;
  color: #FFFFFF;
  line-height: 44rpx;
  text-align: right;
  margin-left: 10rpx;
}
.tl-navigation {
  width: 175rpx;
  height: 60rpx;
  background: #50F5FF;
  border-radius: 83rpx;
  display: flex;
  align-items: center;
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 500;
  font-size: 28rpx;
  color: #454545;
  line-height: 28rpx;
}
.tl-img-48 {
  width: 48rpx;
  height: 48rpx;
  min-width: 48rpx;
  margin: 0 10rpx 0 6rpx;
}
.tl-tips-box {
  width: 80rpx;
  height: 32rpx;
  background: rgba(26,205,232,0.1);
  border-radius: 8rpx;
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 500;
  font-size: 20rpx;
  color: #1ACDE8;
  line-height: 32rpx;
  text-align: center;
  margin-left: 6rpx;
}
.tl-font-24-00 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 400;
  font-size: 22rpx;
  color: rgba(0,0,0,0.5);
  line-height: 32rpx;
  text-align: left;
}
.tl-font-28-34 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 600;
  font-size: 28rpx;
  color: #342A25;
  line-height: 38rpx;
  text-align: left;
}
.tl-font-28-1a {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 400;
  font-size: 28rpx;
  color: #1ACDE8;
  line-height: 44rpx;
  text-align: left;
}
.tl-font-28-45 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 700;
  font-size: 28rpx;
  color: #454545;
}
.tl-img-28 {
  width: 28rpx;
  height: 28rpx;
  min-width: 28rpx;
  margin: 0 6rpx 0 22rpx;
}
.tl-img-44 {
  width: 44rpx;
  height: 44rpx;
  min-width: 44rpx;
  margin: 0 14rpx 0 22rpx;
}
.tl-font-32-16 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 600;
  font-size: 32rpx;
  color: #F16F6F;
  line-height: 44rpx;
  text-align: right;
}
.tl-font-32-1a {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 600;
  font-size: 32rpx;
  color: #1A1A1A;
  line-height: 32rpx;
  text-align: left;
}
.tl-font-32-34 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 400;
  font-size: 32rpx;
  color: #342A25;
  line-height: 32rpx;
  text-align: left;
}
.tl-font-24-93 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 400;
  font-size: 24rpx;
  color: #939393;
  line-height: 24rpx;
  text-align: left;
}
.tl-font-28-93 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 600;
  font-size: 28rpx;
  color: #939393;
  line-height: 38rpx;
  text-align: left;
}
.tl-overflow-hidden {
  height: 100vh !important;
}
.tl-pop-btn {
  position: fixed;
  bottom: 0rpx;
  width: 100%;
  height: 200rpx;
  padding-top: 40rpx;
  background: #ffffff;
  display: flex;
  justify-content: center;
  >view {
    width: 686rpx;
    height: 80rpx;
    background: #454545;
    border-radius: 40rpx;
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 500;
    font-size: 32rpx;
    color: #50F5FF;
    line-height: 80rpx;
    text-align: center;
  }
}
.tl-contac-pop {
  width: 100%;
  padding: 80rpx 32rpx 0;
  .head-item {
    //width: 110rpx;
    width: 100%;
    height: 100%;
    margin: 0 auto 22rpx;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
  }
  .btn-pay {
    width: 686rpx;
    height: 80rpx;
    background: #454545;
    border-radius: 40rpx;
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 500;
    font-size: 32rpx;
    color: #50F5FF;
    line-height: 80rpx;
    text-align: center;
  }
  .btn-info {
    width: 626rpx;
    height: 120rpx;
    background: rgba(26,205,232,0.1);
    border-radius: 100rpx;
    margin: 36rpx auto 0;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
.tl-apply-pop {
  position: relative;
  width: 100%;
  height: auto;
  //max-height: 70vh;
  overflow: scroll;
  display: flex;
  flex-direction: column;
  padding: 0 32rpx 80rpx;
  .apply-pop-item {
    //width: 100%;
    height: 180rpx;
    background: #FFFFFF;
    border: 4rpx solid #F7F7F7;
    display: flex;
    align-items: center;
    padding: 44rpx 40rpx 28rpx 56rpx;
    margin: 0 auto 20rpx;
    border-radius: 24rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;
    .apply-item-left {
      height: 100%;
      display: flex;
      flex-direction: column;
      justify-content: space-evenly;
      font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
      font-weight: 400;
      font-size: 28rpx;
      color: #939393;
      line-height: 44rpx;
      text-align: left;
    }
    .apply-item-right {
      height: 100%;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
    }
  }
  .tl-select-border {
    border: 4rpx solid #2AD1F3;
  }

  .tl-apply-num-box {
    width: 100%;
    padding: 0 32rpx;
    margin-bottom: 46rpx;
    .tl-apply-num-item {
      height: 140rpx;
      display: flex;
      align-items: center;
      border-bottom: 2rpx solid #F6F6F6;
    }
  }
}
.tl-share-pop {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  margin-top: 85rpx;
  .tl-share-pop-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
    font-weight: 400;
    font-size: 24rpx;
    color: rgba(0,0,0,0.5);
    line-height: 32rpx;
    text-align: center;
    >image {
      width: 100rpx;
      height: 100rpx;
      margin-bottom: 18rpx;
    }
  }
}
.page-bg {
  width: 100vw;
  height: auto;
  overflow: hidden;
  background-size: 750rpx auto;
  //background-color: #fff;
  .slot-wrap {
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
  }
  .main-container {
    position: relative;
    width: 100%;
    .banner-box {
      position: relative;
      width: 100%;
      //height: 488rpx;
      //width: 686rpx;
      padding: 0 32rpx;
      height: 483rpx;
      margin: auto;
      //border-radius: 20rpx;
      background-color: #FFFFFF;
      transition: all .3s;
      .tl-img-658 {
        width: 100%;
        height: auto;
        object-fit: cover; /*按图片原有尺寸比例来裁剪*/
        border-radius: 20rpx;
      }
    }
    .content-info {
      position: relative;
      //top: -48rpx;
      top: 0;
      width: 100%;
      // height: 510rpx;
      border-top-left-radius: 12rpx;
      border-top-right-radius: 12rpx;
      padding: 30rpx 32rpx 20rpx;
      background: #FFFFFF;
    }
    .info-owner {
      width: 100%;
      height: 146rpx;
      background: #FFFFFF;
      display: flex;
      align-items: center;
      margin: 20rpx 0;
      padding: 0 32rpx;
      .owner-text {
        display: flex;
        flex-direction: column;
        justify-content: center;
        width: 65%;
      }
      .owner-line {
        width: 0;
        height: 44rpx;
        border: 2rpx solid rgba(0,0,0, 0.05);
        margin-right: 28rpx;
      }
      .owner-contact {
        >image {
          width: 48rpx;
          height: 48rpx;
          min-width: 48rpx;
        }
      }

    }
    .info-apply {
      width: 750rpx;
      //height: 250rpx;
      background: #FFFFFF;
      padding: 20rpx 32rpx;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      .info-apply-title {
        display: flex;
        justify-content: space-between;
        align-items: center;
      }
      .info-apply-list {
        width: 100%;
        height: auto;
        display: flex;
        align-items: center;
        .apply-list-item {
          width: 114.3rpx;
          height: 100%;
          display: flex;
          flex-direction: column;
          align-items: center;
          justify-content: space-between;
        }
      }
    }
    .content-introduce {
      width: 100%;
      height: auto;
      //padding: 0 22rpx;
      padding-top: 36rpx;
      padding-bottom: 20rpx;
      margin-bottom: 20rpx;
      background: #FFFFFF;
      .introduce-title {
        width: 100%;
        text-align: left;
        margin: 0 32rpx 30rpx;
        font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
        font-weight: 600;
        font-size: 28rpx;
        color: #3D3D3D;
        line-height: 38rpx;
      }
      .introduce-content {
        width: 100%;
        height: auto;
      }
    }

    .bottom-menu {
      position: fixed;
      bottom: 0;
      width: 100%;
      //height: 124rpx;
      height: 176rpx;
      padding: 0 32rpx;
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: #FFFFFF;
      box-shadow: 0rpx 0rpx 8rpx 0rpx rgba(147,147,147,0.5);
      z-index: 2;

      .btn-left {
        width: 130rpx;
        display: flex;
        justify-content: space-between;
        align-items: center;
      }

      .btn-right {
        display: flex;
        justify-content: flex-end;
        align-items: center;
      }
    }
  }
}
.tl-share-tips {
  width: 686rpx;
  height: 64rpx;
  padding: 0 20rpx;
  margin: 36rpx auto;
  border-radius: 20rpx;
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 400;
  font-size: 22rpx;
  color: #1ACDE8;
  line-height: 40rpx;
  text-align: left;
  background: rgba(42,209,243,0.1);
}
.tl-circle-exclamation {
  width: 22rpx;
  height: 22rpx;
  min-width: 22rpx;
  border-radius: 50%;
  display: inline-block;
  background-color: #1ACDE8;
  margin-right: 8rpx;
}
.tl-circle-exclamation::before {
  content: "!";
  font-size: 18rpx;
  color: #f6f6f6;
  line-height: 22rpx;
  text-align: center;
  display: block;
}
.tl-circle-exclamation-aggravate {
  background-color: #D2D2D2!important;
  margin-top: 4rpx;
}
.tl-img-info {
  width: 100%;
  height: auto;
}
.tl-info-box {
  width: 100%;
  height: auto;
  position: relative;
}
.tl-info-image {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%,-50%);
  width: 80rpx;
  height: 80rpx;
  z-index: 1;
}
.tl-info-video {
  width: 100%;
  //height: auto;
  overflow: hidden;
  display: block;
}
button {
  /* 清除默认边框 */
  border: 0;
  outline: none;
  padding: 0;
  margin: 0;
  /*清除默认背景 */
  background-color: transparent;
  border-radius: 0;
}
</style>