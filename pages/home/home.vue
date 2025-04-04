<template>
  <view class="page-bg">
    <view class="main-container">
      <!-- 首页顶部和logo -->
      <u-navbar :is-back="false" class="home-top2" :border-bottom="false" :background="navBackground">
        <image v-if="isMessageReminding" @tap="goNotice" class="inform-icon isInformAnimation" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/is_inform_icon.png" />
        <image v-else @tap="goNotice" class="inform-icon" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/inform_icon.png" />
        <view class="search-input" @tap="goToSearch">
          <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/search_icon.png"
                 class="search-icon" />
          <text class="tl-font-28-f5">搜索活动...</text>
        </view>
      </u-navbar>
      <!-- 首页轮播图 -->
      <template>
        <bannerBox class="tl-flex-row-center2 mb-20" :curTemp="temp" :banners="bannerList"></bannerBox>
      </template>

      <!-- 筛选栏 -->
      <view class="box" :class="['topBox',temp?'isActive-opacity':'active-opacity']" :style="{top:temp?navHeight:0}">
        <activityBox :activitys="activityTypeList" :set-border-radius="setBorderRadius"
                     @onChangeaCtivity="onChangeaCtivity" :setTop="'0'" :curType="1">
        </activityBox>
        <view class="activity-filtrate" :class="['menu-box '+(isShowMask?'':'hide'),(temp?'tl-filtrate-padding':'')]">
          <view class="filtrate-location" @tap="onCalendar">
            <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/location_blue_icon.png"></image>
            <text>定位</text>
          </view>
          <u-dropdown class="u-dropdown" ref="uDropdownOne" title-size="24" menu-icon-size="24" @open="onDropdownOpen" @close="onDropdownClose">
            <u-dropdown-item :title="curCondition0.name?curCondition0.name:'全部类型'">
              <view class="slot-content2">
                <view class="tooltip">
                  <view class="tl-font-28 mb-20" v-for="(item,index) in ditchBtns" :key="index"
                        :class="item.isSelect?'tooltip-title-active':'tooltip-title'"
                        @tap="onChangeDitch(index)">
                    {{item.name}}
                  </view>
                </view>
                <view class="tl-wz-btn">
                  <u-button :hair-line="false" :custom-style="customStyle" shape="square" size="default" :ripple="true"
                            @click="typeReset(1)">重置</u-button>
                  <u-button :hair-line="false" :custom-style="customStyle2" shape="square" size="default" :ripple="true"
                            ripple-bg-color="#42D3AD" @click="onConditionConfirm0">确定</u-button>
                </view>
              </view>
            </u-dropdown-item>
            <u-dropdown-item title="时间">
              <view class="slot-content">
               <homeCalendar :mode="alendarType" @change="changeCalendar" @resetBtn="resetBtn"
                              :change-year="false" :change-month="false" ref="changeTimeOne" max-date="2080-01-01"
                              active-bg-color="linear-gradient( 90deg, #F9A9F2 0%, #B9FBFF 100%)"
                              bg-active-color="linear-gradient( 90deg, #B9FBFF 0%, #F9A9F2 100%)" active-color="#454545"
                              range-bg-color="rgba(40,200,209, 0.2)" range-color="#454545" btn-type="default">
                  <view slot="tooltip" class="tooltip" style="padding: 25rpx 0">
                    <view class="tl-font-28" :class="calendarBtns[0].isSelect?'tooltip-title-active':'tooltip-title'"
                          @tap="onChangeTime('weekend',0)">
                      本周末
                    </view>
                    <view class="tl-font-28" :class="calendarBtns[1].isSelect?'tooltip-title-active':'tooltip-title'"
                          @tap="onChangeTime('week',1)">
                      一周内
                    </view>
                    <view class="tl-font-28" :class="calendarBtns[2].isSelect?'tooltip-title-active':'tooltip-title'"
                          @tap="onChangeTime('month',2)">
                      一月内
                    </view>
                  </view>
                </homeCalendar>
              </view>
            </u-dropdown-item>
            <u-dropdown-item :title="curCondition2.name?curCondition2.name:'综合排序'">
              <view class="slot-content2">
                <view class="tooltip" >
                  <view class="tl-font-28" v-for="(item,index) in wzBtns" :key="index"
                        :class="[item.isSelect?'tooltip-title-active':'tooltip-title',index === 3?'tooltip-title-margin-top':'']"
                        @tap="onChangeWz(index)">
                    {{item.name}}
                  </view>
                </view>
                <view class="tl-wz-btn">
                  <u-button :hair-line="false" :custom-style="customStyle" shape="square" size="default" :ripple="true"
                            @click="conditionReset(1)">重置</u-button>
                  <u-button :hair-line="false" :custom-style="customStyle2" shape="square" size="default" :ripple="true"
                            ripple-bg-color="#42D3AD" @click="onConditionConfirm2()">确定</u-button>
                </view>
              </view>
            </u-dropdown-item>
          </u-dropdown>
        </view>
      </view>

      <view class="topTempFixed" :class="temp?'topTempFixedShow':''"></view>

      <!-- 活动列表 -->
      <view @tap="goDetails">活动item</view>
    </view>

    <tabbarCustom :cur-index="0"></tabbarCustom>
  </view>
</template>

<script>
import bannerBox from "../../components/home/banner-box.vue"
import activityBox from "../../components/home/activity-box.vue"
import homeCalendar from "../../components/home/home-calendar.vue";
import tabbarCustom from "../../components/index/tabbar-custom.vue";
export default {
  components: {
    tabbarCustom,
    bannerBox,
    activityBox,
    homeCalendar
  },
  data() {
    return {
      navBackground: {
        // background: '#DCFFEB',
        backgroundImage: 'linear-gradient( 180deg, #d3e8f8 0%, #d5e9f8 5%)'
      },
      setBorderRadius: '15rpx',
      fullLoading: false,//loding
      isNoMsg: false,
      alendarType: "range",
      temp: 0,
      myScroll: 0,
      statusBarHeight: 0,
      curTop: '164rpx',
      user: {
        avatarUrl: 'https://mmbiz.qpic.cn/mmbiz/icTdbqWNOwNRna42FI242Lcia07jQodd2FJGIYQfG0LAJGFxM4FbnQP6yfMxBgJ0F3YRqJCJ1aPAK2dQagdusBZg/0',
        nickname: '微信用户',
      },
      myHome: "", //我的社区
      comPage: 1,
      goodsPop: false, //选择社区
      // goodsPop: true, //选择社区

      showRule: true,
      isDisable: false,
      isCheck: false,
      communityList: [], // 社区数据
      specList: [],
      specChildList: [],
      selectedInfo: {
        specSelected: [],
        goodsInfo: {},
      },
      noClick: true,
      filterResult: "",

      page: 1,
      // 每页数据数
      pageSize: 10,
      // 是否还有更多数据
      noMoreData: false,
      activityLists: [], //活动列表
      latitude: "", // 纬度
      longitude: "", // 经度
      classify_id: 0, // 分类
      weight_id:5, //5:默认热门 4:邻里
      free: false, //免费
      currency: false, //玩籽
      within_3_km: false, //三公里内
      start_time: "", // 开始时间
      end_time: "", //结束时间
      // apply_price: "", // 价格
      // distance: "", //距离
      // start_day_of_week: "", //活动开始时间星期几
      isBottom: true, // 是否需要上拉加载更多
      weekList: [],
      weekListIndex: [{
        id: 1,
        name: '价格',
        active: false
      },
        {
          id: 2,
          name: '玩籽',
          active: false
        },
        {
          id: 3,
          name: '附近',
          active: false
        }
      ],
      isShowCalendar: false,
      bannerList: [
        {
          name: "",
          sort: 0,
          title: "",
          url: "http://files.uefun.net/upload/picture/banner2@2x.png"
        }
      ],
      activityTypeList: [
        {
          id: 0,
          select: true,
          sort: 0,
          thumb: "",
          title: "推荐"
        },
        {
          id: 1,
          select: false,
          sort: 1,
          thumb: "https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/home/ball.png",
          title: "运动户外"
        },
        {
          id: 2,
          select: false,
          sort: 2,
          thumb: "https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/home/9.png",
          title: "体验工坊"
        }
      ],
      ditchBtns:[
        {
          id:0,
          name:'全部类型',
          isSelect: true
        },
        {
          id:1,
          name:'线下活动',
          isSelect: false
        },
        {
          id:2,
          name:'线上活动',
          isSelect: false
        },
      ],
      calendarBtns: [
        {
          isSelect: false
        },
        {
          isSelect: false
        },
        {
          isSelect: false
        },
      ],
      wzBtns: [
        {
          id:0,
          name:'综合排序',
          isSelect: true
        },
        {
          id:1,
          name:'玩籽抵扣',
          isSelect: false
        },
        {
          id:2,
          name:'低价优先',
          isSelect: false
        },
        {
          id:3,
          name:'高价优先',
          isSelect: false
        },
      ],
      status: 'loadmore',
      iconType: 'flower',
      loadText: {
        loadmore: '轻轻上拉加载更多',
        loading: '努力加载中',
        nomore: '已经到底啦'
      },
      isCommunityAuthentication: '',//是否认证小区
      isLoding: true,
      lodingStatus: 'loading',
      isShowMask:false,
      customStyle: {
        color: '#000000',
        background: '#FFFFFF',
        width:'200rpx',
        height:'80rpx',
        border:'2rpx solid rgba(0,0,0,0.1)',
        borderRadius: '40rpx'
      },
      customStyle2: {
        color: '#50F5FF',
        background: '#454545',
        width:'450rpx',
        height:'80rpx',
        borderRadius: '80rpx'
      },
      curCondition0:'',//当前筛选项0
      curCondition1:'',//当前筛选项1
      curCondition2:'',//当前筛选项2
      isMessageReminding:'',//消息提醒
      isTips:false,//定位提醒
    };
  },
  onLoad() {
    this.$nextTick(() => {
      this.getMyScroll();
      this.refreshResult(true);
    });

  },
  onShow() {
  },
  onUnload() {
    console.log("onUnload")
    this.activityLists = [];
  },
  mounted() {
    console.log("刷新会执行")
  },
  onPageScroll(e) {
    if (e.scrollTop > this.myScroll) {
      this.temp = 1
    } else {
      this.temp = 0
    }
  },
  watch: {
    temp(newVal, oldVal) {
      console.log("当前22", newVal, oldVal);
      this.$refs.uDropdownOne.getContentHeight();
    }
  },
  computed: {
    navHeight() {
      let statusBarHeight = 0;
      // #ifdef H5
      statusBarHeight = 44; // 例如在 H5 中，通常设置为44px
      // #endif
      return statusBarHeight + 'px'
    },
  },
  methods: {
    goNotice() {
    },
    conditionReset(isRefresh=false) {
      this.wzBtns.map((item, index) => {
        if(index === 0) {
          item.isSelect = true;
        }else {
          item.isSelect = false;
        }
      })
      this.curCondition2 = this.wzBtns[0];
      this.setCalendarClose();
      console.log("conditionReset")
      if(isRefresh) {
        // this.result();
      }
    },
    timeReset() {
      console.log("timeReset", this.calendarBtns)
      this.calendarBtns.map((item, index) => {
        item.isSelect = false;
      })
      this.start_time = "";
      this.end_time = "";
      this.curCondition1 = "";

      this.$refs.changeTimeOne.init();//清除日历区间
      this.start_time = "";
      this.end_time = "";
      this.curCondition1 = "";
      this.$refs.uDropdownOne.highlight();
    },
    typeReset(isRefresh=false) {
      this.ditchBtns.map((item, index) => {
        if(index === 0) {
          item.isSelect = true;
        }else {
          item.isSelect = false;
        }
      })
      this.curCondition0 = this.ditchBtns[0];
      this.setCalendarClose();
      if(isRefresh) {
        // this.result();
      }
    },
    getMyScroll() {
      //获取目标板块到顶部的距离
      const that = this
      let statusBarHeight = 0;
      //条件编译
      // #ifdef H5
      statusBarHeight = 44; // 例如在 H5 中，通常设置为44px
      // #endif
      uni.createSelectorQuery().in(this).select('.box').boundingClientRect(res => {
        console.log("获取目标板块到顶部的距离", res,statusBarHeight)
        let height = statusBarHeight;
        that.myScroll = res.top - height;
      }).exec();
    },
    //重置筛选项
    resetFilterItem(type=1,initialize=false) {
      if(type === 1) {
        this.classify_id = 0;
      }
      console.log("重置选项", initialize)
      // this.selectTtemChoice();
      this.start_time = '';
      this.end_time = '';
      if(initialize)return;
      // 重置选择日历
      this.timeReset();
      //重置筛选项
      this.typeReset();
      this.conditionReset();
    },
    onCalendar() { //重新定位当前位置
      console.log("onCalendar")
    },
    setCalendarClose() {
      this.$refs.uDropdownOne.close();
      this.isShowMask = false;
      // this.onDropdownClose();

    },
    changeCalendar(e) { //时间筛选
      this.$refs.uDropdownOne.highlight(1);
      console.log("changeCalendar", e)
      // 关闭下拉
      this.setCalendarClose();
      // this.result();
    },
    onChangeDitch(curIndex) {
      this.ditchBtns.map((item, index) => {
        if (curIndex == index) {
          item.isSelect = true
        } else {
          item.isSelect = false
        }
      })
    },
    onChangeTime(type, curIndex) {
      this.calendarBtns.map((item, index) => {
        if (curIndex == index) {
          item.isSelect = true
        } else {
          item.isSelect = false
        }
      })
      this.$refs.changeTimeOne.setTime(type);
    },
    onChangeWz(curIndex) {
      this.wzBtns.map((item, index) => {
        if (curIndex == index) {
          item.isSelect = true
        } else {
          item.isSelect = false
        }
      })
    },
    onConditionConfirm0() {//筛选项确认0
      let item = this.ditchBtns.filter((item, index) => {
        return item.isSelect === true;
      });
      this.curCondition0 = item[0];
      this.setCalendarClose();
      // this.result();
    },
    onConditionConfirm2() {//筛选项确认2
      let item = this.wzBtns.filter((item, index) => {
        return item.isSelect === true;
      });
      this.curCondition2 = item[0];
      this.setCalendarClose();
      // this.result();
    },
    onChangeaCtivity(index) { //切换分类展示状态
      console.log("onChangeaCtivity", index, this.activityTypeList, this.activityTypeList[index])
      this.activityTypeList.map((item, index) => {
        item.select = false;
      })
      this.activityTypeList[index].select = true;
    },
    resetBtn() {
      this.calendarBtns.map((item, index) => {
        item.isSelect = false;
      })
      this.start_time = "";
      this.end_time = "";
      this.curCondition1 = "";

      this.setCalendarClose();
      // this.result();
    },
    onDropdownClose(e) {
      console.log("onDropdownClose",e,this.start_time,this.end_time)
      switch(e) {
        case 0:
          if(this.curCondition0) return;
          this.typeReset();
          break;
        case 1:
          if(this.start_time && this.end_time) return;
          this.timeReset();
          break;
        case 2:
          if(this.curCondition2) return;
          this.conditionReset();
          break;
      }
      this.isShowMask = false;
    },
    onDropdownOpen() {
      this.isShowMask = true;
    },
    // 筛选参数组装
    result(type=0) {
      this.page = 1;
      this.noMoreData = false;
      this.activityLists = []; //数组置空操作
    },
    refreshResult(init=false) {
      console.log("refreshResult",init);
      this.page = 1;
      this.noMoreData = false;
      this.activityLists = []; //数组置空操作
      this.resetFilterItem(1,init);
    },
    // 转时间格式
    timeStamp(value) {
      let date = new Date(value * 1000); //时间戳为10位需*1000，时间戳为13位的话不需乘1000
      let year = date.getFullYear();
      let month = ("0" + (date.getMonth() + 1)).slice(-2);
      let sdate = ("0" + date.getDate()).slice(-2);
      let hour = ("0" + date.getHours()).slice(-2);
      let minute = ("0" + date.getMinutes()).slice(-2);
      let second = ("0" + date.getSeconds()).slice(-2);
      // 拼接
      //let result = year + "." + month + "." + sdate + " " + hour + ":" + minute //+ ":" + second;
      let result = month + "." + sdate; //+ ":" + second;
      // 返回
      return result;
    },
    // 跳转到搜索页
    goToSearch() {

    },
    goLogin() {
      uni.navigateTo({
        url: "/pages/login/login",
      });
    },
    goDetails() {
      uni.navigateTo({
        url: "/pages/details/details",
      });
    }
  }
}
</script>

<style lang="scss" scoped>
::v-deep .u-dropdown__menu {
  justify-content: flex-end !important;
}
::v-deep .u-dropdown__menu__item {
  flex: none !important;
  margin-right: 15rpx;
}
::v-deep .u-dropdown__menu__item .u-flex {
  height: 60rpx;
  padding: 0 20rpx;
  //background: rgba(255,255,255,0.6);
  background: #F7F7F7;
  border-radius: 60rpx;
}
@keyframes shake {
  0% { margin-left: 3px; }
  25%{  margin-left: 0px; }
  50% {  margin-left: 3px; }
  75% {  margin-left: 0px; }
  100% {  margin-left: 3px; }
}

.tl-section-2 {
  height: 600rpx;
  padding-top: 80rpx;
}

::-webkit-scrollbar {
  width: 0;
  height: 0;
  color: transparent;
}

.isActive-opacity {
  //opacity: 0;
  position: fixed;
  //transition:top 0.3s;
  animation: shake 0.3s forwards;
}

.active-opacity {
  //opacity: 1;
  position:none;
}

.tl-font-28-f5 {
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: 400;
  font-size: 28rpx;
  color: #C7C7C7;
  line-height: 32rpx;
  text-align: left;
}

.tooltip {
  //margin-top: 10rpx;
  //padding: 62rpx 0 112rpx;
  padding: 44rpx 0 60rpx;
  //height: 60rpx;
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  .tooltip-title {
    background: rgba(26,205,232,0.1);
    padding: 0 50rpx;
    margin: 0 15rpx;
    border-radius: 12rpx;
    line-height: 60rpx;
    color: #454545;
    //border: 2rpx solid #707070;
  }
  .tooltip-title-margin-top {
    margin-top: 40rpx;
  }
  .tooltip-title-active {
    padding: 0 50rpx;
    margin: 0 15rpx;
    border-radius: 12rpx;
    line-height: 60rpx;
    color: #454545;
    //background: #42D3AD;
    border-radius: 12rpx;
    background: linear-gradient( 90deg, #F9A9F2 0%, #B9FBFF 100%);
  }
}

.tl-font-28 {
  font-size: 28rpx;
  font-family: Source Han Sans SC-Medium, Source Han Sans SC;
  font-weight: 500;
}

.logo {
  width: 120rpx;
  height: 120rpx;
}

.tl-img-511-418 {
  width: 511rpx;
  height: 418rpx;
  display: block;
  margin: auto;
}

.user-avatar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20rpx;

  .avatar-btn {
    width: 132rpx;
    height: 132rpx;
    padding: 0;

    .avatar {
      width: 100%;
      height: 100%;
    }
  }
}

.tl-font-weight {
  font-weight: 600;
}
.page-bg {
  width: 100vw;
  height: auto;
  min-height: 100vh;
  overflow: hidden;
  //background-color: #ffffff;
  background: linear-gradient( 180deg, rgba(112,191,255,0.2706) 0%, rgba(226,156,228,0) 80%);
  .main-container {
    width: 100%;
    margin: 0 auto;

    .home-top {
      position: fixed;
      //top: calc(var(--status-bar-height) + 50rpx);
      z-index: 2;
      margin-left: 20rpx;

      .appLogo {
        font-size: 38rpx;
        font-family: myFont;
        color: #333333;
      }

      .search-icon {
        width: 32rpx;
        height: 32rpx;
        margin-left: 20rpx;
        color: #F5F5F5;
      }

      .search-input {
        //width: 450rpx;
        // height: 72rpx;
        height: 100%;
        display: flex;
        justify-content: flex-start;
        align-items: center;
        border: 2rpx solid #ccc;
        border-radius: 10rpx;
        background: #4D4D4D;
        opacity: 0.5;

        .search-icon {
          width: 32rpx;
          height: 32rpx;
          margin-left: 28rpx;
          margin-right: 32rpx;
          color: #F5F5F5;
        }
      }
    }

    .home-top2 {
      z-index: 2;
      .inform-icon {
        width: 48rpx;
        height: 48rpx;
        min-width: 48rpx;
        margin-right: 26rpx;
        margin-left: 30rpx;
      }
      .search-input {
        width: 406rpx;
        height: 64rpx;
        background: #FFFFFF;
        border-radius: 40rpx;
        border: 2rpx solid #454545;
        display: flex;
        justify-content: flex-start;
        align-items: center;

        .search-icon {
          width: 32rpx;
          height: 32rpx;
          margin-left: 20rpx;
          margin-right: 10rpx;
        }
      }
    }


    .topBox {
      width: 100%;
      //background-color: #e7f8ed;
      background: linear-gradient( 180deg, #FFFFFF 0%, #fcfcfc 100%);
      z-index: 1;
      border-top-left-radius: 24rpx;
      border-top-right-radius: 24rpx;
      padding: 32rpx 0 20rpx;
      .activity-filtrate {
        position: relative;
        width: 100%;
        //height: 110rpx;
        // background-color: #ffffff;
        // background-color: #7DBFFF;
        //padding: 0 36rpx;
        display: flex;
        align-items: center;
        justify-content: space-between;
        .filtrate-location {
          position: absolute;
          left: 32rpx;
          height: 60rpx;
          padding: 0 20rpx;
          display: flex;
          justify-content: center;
          align-items: center;
          //background: rgba(255,255,255,0.6);
          background: #F7F7F7;
          border-radius: 60rpx;
          font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
          font-weight: 500;
          font-size: 28rpx;
          color: #3D3D3D;
          line-height: 32rpx;
          text-align: left;
          z-index: 999;
          >image {
            width: 32rpx;
            height: 32rpx;
            min-width: 32rpx;
            margin-right: 12rpx;
          }
        }
      }
    }
    .topTempFixed {
      display: none;
      width: 100%;
      height: 210rpx;
    }
    .topTempFixedShow {
      display: block;
    }
    .activity-List {
      width: 100%;
      height: auto;
      //padding-top: 20rpx;
      //background: linear-gradient( 180deg, #e7f8ed 0%, #F4F4F4 100%);
      background: linear-gradient( 180deg, rgba(251, 251, 251,0.97) 0%, rgba(226,156,228,0) 44%);
    }

  }

  .title {
    position: relative;
    height: 88rpx;
    text-align: center;
    line-height: 88rpx;
    font-size: 32rpx;
    font-weight: 500;
    color: #292929;
    border-bottom: 1rpx solid #f3f3f3;
  }

  .tl-div-line {
    width: 254rpx;
    border-bottom: 10rpx solid #f2c827;
  }

  .title .close {
    position: absolute;
    right: 30rpx;
    top: 50%;
    -webkit-transform: translateY(-50%);
    transform: translateY(-50%);
  }

  .value {
    display: flex;
    flex-wrap: wrap;
    padding: 0 0 40rpx 0;
    border-bottom: 2rpx solid #dcdcdc;

    text {
      display: block;
      border-radius: 25rpx;
      border: 2rpx solid #bcbcbc;
      margin: 24rpx 18rpx 0 0;
      padding: 6rpx 18rpx;
    }

    .selected {
      background: #f2c827;
      border-radius: 23rpx;
      font-size: 28rpx;
      font-family: PingFangSC-Regular, PingFang SC;
      font-weight: 400;
      color: #292929;

      /*border-color: #f86341;
      color: #ffffff;
      background-color: #f86341;*/
    }
  }

  .mask-box {
    width: 100vw;
    height: 100%;
    position: fixed;
    top: 0;
    left: 0;
    z-index: 100;
    background-color: rgba($color: #000000, $alpha: 0.5);
    display: flex;
    justify-content: center;
    align-items: center;

    .mask-content {
      width: 560rpx;
      height: 660rpx;
      display: flex;
      flex-direction: column;
      justify-content: space-evenly;
      align-items: center;
      background: #fff;
      border-radius: 12rpx;

      .home-window {
        //margin-top: 85rpx;
        width: 242rpx;
        height: 242rpx;
      }

      .home-window2 {
        width: 100%;
        height: 786rpx;
      }

      .home-vip-img {
        width: 666rpx;
        height: 836rpx;
      }

      .home-window-close {
        width: 84rpx;
        height: 84rpx;
        margin: 20rpx 0;
      }

      .home-red-wine {
        width: 664rpx;
        height: 692rpx;
      }
    }

    .mask-grouping-box {
      width: 630rpx;
      height: auto;
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      align-items: flex-end;
      position: relative;
      top: 0;
      bottom: 0;
      left: 0;
      right: 0;
      margin: auto;

      .home-grouping-bg {
        width: 630rpx;
        height: 474rpx;
      }

      .home-grouping-close {
        width: 88rpx;
        height: 88rpx;
        position: absolute;
        bottom: 0;
        left: 0;
        right: 0;
        margin: auto;
      }

      .grouping-list {
        width: 582rpx;
        height: auto;
        background: #ff3a23;
        opacity: 1;
        border-radius: 0px 0px 32rpx 32rpx;
        display: flex;
        flex-direction: column;
        justify-content: flex-start;
        align-items: center;
        position: relative;
        top: -150rpx;
        padding-bottom: 30rpx;
      }
    }
  }

  .tl-btn-630 {
    width: 400rpx;
    height: 68rpx;
    line-height: 68rpx;
    border-radius: 10rpx;
    font-size: 28rpx;
    font-family: PingFangSC-Medium, PingFang SC;
    font-weight: 500;
    margin-bottom: 30rpx;
  }

  .tl-btn-630-c {
    box-sizing: border-box;
    width: 400rpx;
    height: 68rpx;
    line-height: 68rpx;
    border-radius: 10rpx;
    font-size: 28rpx;
    font-family: PingFangSC-Medium, PingFang SC;
    font-weight: 500;
    //margin-bottom: 30rpx;
    border: 3rpx solid #323333;
  }
}
.slot-content {
  width: 750rpx;
}
.slot-content2 {
  width: 750rpx;
  padding-bottom: 40rpx;
  //background: linear-gradient( 180deg, #e5f9ec 0%, #fefefe 100%) !important;
  background: #ffffff !important;
  border-bottom-left-radius: 24rpx;
  border-bottom-right-radius: 24rpx;
}
.tl-filtrate-padding {
  margin: 26rpx 0 20rpx;
}

.menu-box {
  &.hide {
    ::v-deep .u-dropdown__content {
      display: none;
    }
  }
}
.tl-wz-btn {
  display: flex;
  justify-content:space-around;
}
.tl-authentication-btn {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  bottom: -120rpx;
  width: 120rpx;
  height: 60rpx;
  background: #50F5FF;
  border-radius: 30rpx;
  color: #454545;
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: bold;
  line-height: 60rpx;
  font-size: 27rpx;
  text-align: center;
}
</style>