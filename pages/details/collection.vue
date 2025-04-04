<template>
  <view class="page-bg">
    <!-- 动态添加输入框模块   -->
    <view class="community-box">
      <block v-for="(item,index) in conLists" :key="index">
        <view class="community-item" @tap.stop="item.select && clickChangeOption(index)">
          <view class="tl-font-32-93">{{item.name}}<text v-if="item.tips" class="tl-font-24-coc">{{item.tips}}</text></view>
         <view class="tl-flex-row-start">
           <text v-if="item.select">{{item.value}}</text>
           <view v-else  @tap.stop="onInputPop(item,index)">
             <text>{{item.value?item.value:'请输入'}}</text>
             <!--           <input v-else-if="item.inputType == 1" class="tl-input-width" type="text" placeholder='请输入' v-model="item.value"></input>-->
             <!--           <input v-else-if="item.inputType == 2" class="tl-input-width" type="number" maxlength="11" placeholder='请输入' v-model="item.value"></input>-->
           </view>

           <view class="tl-mod">
             <view class="tl-to_right tl-center-h-v"></view>
           </view>
         </view>
        </view>
<!--        <view v-if="item.isSelect" class="community-item-auxiliary" v-for="(data,i) in item.select" :key="i"-->
<!--              @tap="clickSelectItem(item,data)">-->
<!--          <text class="tl-font-32-ff">{{data.number}}</text>-->
<!--          <text class="tl-font-32-93" decode>{{'&nbsp;' + data.value}}</text>-->
<!--        </view>-->

        <radio-group  v-if="item.isSelect" class="community-item-auxiliary" @change="accountChange($event,item,data)" v-for="(data,i) in item.select" :key="i">
          <view>
            <text class="tl-font-32-ff">{{data.number}}</text>
            <text class="tl-font-32-93" decode>{{'&nbsp;' + data.value}}</text>
          </view>
          <radio :value="i" iconColor="#454545" style="transform:scale(0.8)" color="#1ACDE8" :checked="i === curAccountIndex"/>
        </radio-group>
      </block>
    </view>

    <view class="tl-footer">
      <view class="tl-footer-gradual-btn" @tap="confirm">提交</view>
    </view>
    <view class="tl-h200"></view>

    <inputPop :shortTerm="curInputPop" :setHeight="270" @changePop="applyChangePop" @saveMsg="onRename"
              :title="'修改' + curInputData.name" :placeholder="'请输入' + curInputData.name" :curIntroduce="curInputData.value"
              :inputType="curInputData.inputType == 1?'text' : 'number'"
              :setInputHeight="88" :maxlength="curInputData.inputType == 1?20:11">
    </inputPop>
  </view>
</template>

<script>
import typeTips from "../../components/activitv/type-tips.vue";
import inputPop from "../../components/publish/input-pop.vue";

export default {
  components: {inputPop, typeTips},
  data() {
    return {
      //默认选项相关
      conLists: [
        // {
        //   name:'昵称',
        //   value:'',
        //   isNew:false,
        //   inputType:1,//文字输入
        // },
        // {
        //   name:'性别',
        //   value:'',
        //   isNew:false,
        //   isSelect:true,
        //   select:[
        //     {
        //       number:'A',
        //       value:'男'
        //     },
        //     {
        //       number:'B',
        //       value:'女'
        //     }
        //   ]
        // },
        // {
        //   name:'真实姓名',
        //   value:'',
        //   isNew:false,
        //   inputType:1,//文字输入
        // },
        // {
        //   name:'基础疾病信息',
        //   value:'',
        //   isNew:false,
        //   inputType:1,//文字输入
        //   tips:'(没有则填无)',
        // },
        // {
        //   name:'手机号码',
        //   value:'',
        //   isNew:false,
        //   inputType:2,//手机号码
        // },
      ],
      activity_id:'',//活动id
      curId:'',//当前问卷的记录id
      curType:'',//当前进入页面的类型
      curAccountIndex:'',
      curInputPop:false,//是否显示修改昵称弹窗
      curInputData:{},//当前修改的项
      curInputIndex:0,//当前修改的项的索引
      isTask:0,
    }
  },
  onLoad(options){
    this.activity_id = options.id;
    if(options.tempData) {
      this.conLists = JSON.parse(decodeURIComponent(options.tempData))
      this.$log("当前已填写问卷的数据",this.conLists)
    }else if (options.type == 1) {
      // type = 1编辑自己填写过的问卷
      this.curType = options.type;
      this.getQuestionnaire(options.type);
    }else if (Number(options.isTask) === 1) {
      // isTask = 1任务问卷填写
      this.isTask = Number(options.isTask);
      this.getTaskQuestionnaire();
    }
    else {
      this.$log("当前未填写问卷")
      this.getQuestionnaire();
    }
  },

  onShow(){

  },

  methods: {
    onInputPop(item,index) {
      this.curInputData = item;
      this.curInputIndex = index;
      this.curInputPop = true;
      this.$log("当前修改的项",this.curInputData)
    },
    applyChangePop(e) {
      this.curInputPop = e;
      if(!e) {
        this.curInputData = {};

      }
    },
    onRename(value) {
      this.conLists[this.curInputIndex].value = value;
    },
    accountChange(e,data,item) {
      this.$log("accountChange",e)
      this.curAccountIndex = Number(e.detail.value);
      data.value = item.value;
    },
    getQuestionnaire(type=0) {
      this.$log("获取问卷",this.activity_id)
      let params = {
        act_id:this.activity_id,
      }
      let api = 'getQuestionnaire';
      if(type == 1) {
        this.$log("获取填写过的问卷")
        api = 'getFilledQuestionnaire';
      }
      this.$http.get(api, params,true,{callBakc:(res)=>{
          if(res.data.code === 200)return;
          this.$common.toast(res.data.message,3)
          setTimeout(()=>{
            uni.navigateBack({
              delta: 2
            });
            uni.$emit('refreshDetails');
          },800)
        }}).then((res) => {
          let data = res.data.data;
          this.$log("获取问卷成功0",data)
          if(!data) {
            setTimeout(()=>{
              uni.navigateBack({
                delta: 2
              });
              uni.$emit('refreshDetails');
            },800)
            return
          }
          this.curId = data.id;
          this.conLists = JSON.parse(data.user_info_table);
          this.$log("获取问卷成功1",this.conLists)
      })
    },
    getTaskQuestionnaire() {
      this.$log("获取任务问卷",this.activity_id)
      let params = {
        tribe_task_id:this.activity_id,
      }
      this.$http.get("getTaskQuestionnaire", params,true,{callBakc:(res)=>{
          if(res.data.code === 200)return;
          this.$common.toast(res.data.message,3)
          setTimeout(()=>{
            uni.navigateBack({
              delta: 1
            });
            uni.$emit('refreshDetails',{status:0});
          },800)
        }}).then((res) => {
        let data = res.data.data;
        this.$log("获取问卷成功0",data)
        if(!data) {
          setTimeout(()=>{
            uni.navigateBack({
              delta: 1
            });
            uni.$emit('refreshDetails',{status:0});
          },800)
          return
        }
        this.curId = data.id;
        this.conLists = JSON.parse(data.user_info_table);
        this.$log("获取问卷成功1",this.conLists)
      })
    },
    confirm() {
      // 问卷检测
      for (let i = 0;i <this.conLists.length;i++) {
        let item = this.conLists[i];
        this.$log("当前的值",item)
        if(!item.value) {
          let name = item.name;
          this.$common.toast("请填写" + name,3)
          return
        }
      }
      let collection = JSON.stringify(this.conLists)
      if(this.isTask === 1) {
        this.submitTaskQuestionnaire(collection);
        return;
      }
      this.submitQuestionnaire(collection);

      // if(this.curType == 1) {
      //   this.submitQuestionnaire(collection);
      // }else {
      //   let pages = getCurrentPages(); //获取所有页面栈实例列表
      //   this.$log("pages.length",pages)
      //   if (pages.length > 1) {
      //     let nowPage = pages[pages.length - 1]; //当前页页面实例
      //     let prevPage = pages[pages.length - 2]; //上一页页面实例
      //     prevPage.$vm.collection = this.conLists; //修改上一页data里面的参数
      //     uni.navigateBack({ //uni.navigateTo跳转的返回，默认1为返回上一级
      //       delta: 1
      //     });
      //   } else {
      //     this.$common.toast('页面进程错误',3)
      //   }
      // }
    },
    //提交问卷
    submitQuestionnaire(data) {
      let self = this;
      let params = {
        user_info_table:data,
        act_id:self.activity_id,
        id:self.curId
      }
      this.$log("提交问卷的参数",params)
      this.$http.post('submitQuestionnaire',params).then((res)=>{
        if(res.data.code == 200) {
          this.$log("问卷提交成功",res.data)
          this.$common.toast(res.data.message)
          setTimeout(()=>{
            uni.navigateBack({
              delta: 2
            });
            uni.$emit('refreshDetails');
          },800)
        }else {
          this.$common.toast(res.data.message,2)
        }
      })
    },
    submitTaskQuestionnaire(data) {
      let self = this;
      let params = {
        user_info_table:data,
        tribe_task_id:self.activity_id,
        // id:self.curId
      }
      this.$log("提交问卷的参数",params)
      this.$http.post('createTaskApplyQuestionnaire',params).then((res)=>{
        if(res.data.code == 200) {
          this.$log("问卷提交成功",res.data)
          // this.$common.toast(res.data.message)
          setTimeout(()=>{
            uni.navigateBack({
              delta: 1
            });
            uni.$emit('refreshDetails',{status:1});
          },800)
        }else {
          this.$common.toast(res.data.message,2)
        }
      })
    },
    // 打开关闭选项
    clickChangeOption(index) {
      this.conLists[index].isSelect = !this.conLists[index].isSelect;

      this.$log("打开关闭选项",this.conLists[index])
    },
    // 单选题选择
    clickSelectItem(option,item) {
      this.$log("单选",option,item)
      option.value = item.value;
    }
  }
}
</script>

<style lang="scss" scoped>
.tl-font-32-93 {
  font-size: 32rpx;
  font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
  font-weight: 600;
  color: rgba(0,0,0,0.8);
  line-height: 32rpx;
}

.page-bg {
  width: 100vw;
  height: auto;
  overflow: hidden;
  background-size: 750rpx auto;

  .tips {
    width: 686rpx;
    margin: 32rpx auto;
  }
  .community-box {
    width: 686rpx;
    height: auto;
    padding: 0 32rpx;
    background: #FFFFFF;
    border-radius: 12rpx;
    margin: 32rpx auto 0;
    .community-item {
      width: 100%;
      height: 120rpx;
      margin: 0 auto;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 2rpx solid rgba(0, 0, 0, 0.05);
    }
    .community-item-auxiliary {
      width: 100%;
      height: 120rpx;
      margin: 0 auto;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 2rpx solid rgba(0, 0, 0, 0.05);
    }
  }
}

.tl-font-24-coc{
  font-size: 24rpx;
  font-family: PingFang SC-Medium-Regular, PingFang SC-Medium;
  font-weight: 400;
  color: #C0C0C0;
}
.tl-font-32-ff {
  font-size: 32rpx;
  font-family: PingFang SC-Bold-Regular, PingFang SC-Bold;
  font-weight: 600;
  color: #767676;
  line-height: 32rpx;
}
.tl-h200 {
  height: 200rpx;
}
.tl-input-width {
  max-width: 250rpx;
  text-align: right;
}
.tl-mod {
  display: flex;
  width: 20rpx;
  height: 20rpx;
  margin-left: 18rpx;
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
</style>

<style>
page {
  background: #F6F6F6;
}
</style>