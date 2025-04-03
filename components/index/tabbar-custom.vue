<template>
  <view style="z-index: 999">
    <u-tabbar :list="tabbar" :mid-button="true" :border-top="false"
              :before-switch="beforeSwitch" icon-size="48" mid-button-size="96" height="110rpx"></u-tabbar>
  </view>
</template>

<script>
export default {
  data() {
    return {
      tabbar:[],
      isPublishShow:false,
    };
  },
  created() {
    console.log("Created")
    this.tabbar = [{
        pagePath: "/pages/home/home",
        iconPath: "/static/tabbar/index_icon.png",
        selectedIconPath: "/static/tabbar/index_icon_select.png",
        text: "首页",
        // count: 2,
        // isDot: true,
      },
      {
        pagePath: "/pages/index/index",
        iconPath: "/static/tabbar/create_icon.png",
        selectedIconPath: "/static/tabbar/create_icon.png",
        text: "",
        midButton: true,
      },
      {
        pagePath: "/pages/my/my",
        iconPath: "/static/tabbar/my_icon.png",
        selectedIconPath: "/static/tabbar/my_icon_select.png",
        text: "我的",
      },
    ]
  },
  components: {
  },
  props: {
    curIndex:{
      type: Number,
      default: 0
    },
  },
  // computed: {
  //   isShortTerm: {
  //     get() {
  //       return this.shortTerm;
  //     },
  //     set(value) {
  //       this.$emit("changePop", value)
  //     }
  //   }
  // },
  methods: {
    beforeSwitch(index) {
      let judge = true;
      console.log("beforeSwitch00",index)
      if(index === 1) {
        console.log("beforeSwitch",index)
        judge = false;
      }
      return judge;
    }
  }
};
</script>

<style lang="scss" scoped>

</style>