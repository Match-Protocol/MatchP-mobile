<template>
  <view class="phone-login">
    <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/nlogo.png" class="tl-img-212"></image>

    <view class="form-login">
      <view class="account-item">
        <image v-show="loginType === 1" class="" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/time.png" />
        <image v-show="loginType === 2" class="" src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/location.png" />
        <u-input style="flex: 1" v-model="form.mobile" placeholder-class="pcs" placeholder='mobile number' />
      </view>
      <view class="prompt-jump" @tap="onChangeType">or
        <text class="tl-text-markers">{{loginType === 1?'use email' : 'use mobile number'}}</text>
      </view>
      <view class='submit-btn' :class="btnColor ? 'active' : ''" @click="submit">
        {{loginType === 1?'Continue With mobile' : 'Continue With Email'}}
      </view>

      <view class='wallet-connect'>
        <image src="https://goin.obs.cn-north-4.myhuaweicloud.com/acticity/common/location.png"/>
        <text>wallet connect</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      btnColor: false,
      jv: [],
      // 获取验证码倒计时
      codetime: 0,
      openCount: true,
      // 验证码id
      msg_id: '',
      form: {
        mobile: '13548011498',
        smsCode: '',
      },
      rules: {
        // 字段名称
        mobile: [{
          required: true,
          message: '请输入手机号',
          trigger: ['change', 'blur'],
        },
          {
            // 自定义验证函数，见上说明
            validator: (rule, value, callback) => {
              // 上面有说，返回true表示校验通过，返回false表示不通过
              // this.$u.test.mobile()就是返回true或者false的
              return this.$u.test.mobile(value);
            },
            message: '手机号码不正确',
            // 触发器可以同时用blur和change
            trigger: ['change', 'blur'],
          }
        ],
        intro: [{
          min: 5,
          message: '简介不能少于5个字',
          trigger: 'change'
        }]
      },
      loginType:1,//1手机2邮箱
    }
  },
  methods: {
    onChangeType() {
      this.loginType = this.loginType === 1 ? 2 : 1;
    },
    // 验证手机号码
    isPhone() {
      let mPattern = /^1[34578]\d{9}$/;
      return mPattern.test(this.form.mobile);
    }
  }
}
</script>

<style lang="scss" scoped>
view{
  line-height: normal;
}
.phone-login {
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 0 64rpx;
  box-sizing: border-box;
  text-align: center;
  background: #FFFFFF;
  .form-login {
    display: flex;
    flex-direction: column;
    align-items: center;
    .account-item {
      width: 622rpx;
      height: 96rpx;
      display: flex;
      align-items: center;
      padding: 0 30rpx;
      border-radius: 48rpx;
      margin-bottom: 74rpx;
      background: #F6F6F6;
      >image {
        width: 48rpx;
        height: 48rpx;
        flex-shrink: 0;
        margin-right: 38rpx;
      }
    }
    .prompt-jump {
      font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
      font-weight: 500;
      font-size: 28rpx;
      color: #666666;
      line-height: 32rpx;
      text-align: center;
      margin-bottom: 58rpx;
    }
    .submit-btn {
      width: 560rpx;
      height: 92rpx;
      line-height: 92rpx;
      text-align: center;
      background: linear-gradient( 90deg, #F9A9F2 0%, #B9FBFF 100%);
      border-radius: 44rpx;
      font-size: 32rpx;
      font-family: Source Han Sans SC-Bold, Source Han Sans SC;
      font-weight: bold;
      color: #464646;
      margin-bottom: 108rpx;
    }
    .wallet-connect {
      width: 560rpx;
      height: 92rpx;
      background: #FFFFFF;
      border-radius: 44rpx;
      border: 2rpx solid rgba(0,0,0,0.1);
      display: flex;
      justify-content: center;
      align-items: center;
      >image {
        width: 48rpx;
        height: 48rpx;
        margin-right: 12rpx;
      }
      >text {
        font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
        font-weight: bold;
        font-size: 32rpx;
        color: #464646;
        line-height: 44rpx;
        text-align: left;
      }
    }
  }
  .active{
    background: #333333;
    color: #FF770F;
  }

  .sms-code-btn {
    border: none;
    font-size: 24rpx;
    font-family: Source Han Sans SC-Normal, Source Han Sans SC;
    font-weight: 400;
    color: #FF770F;
  }
}

.u-hairline-border:after {
  border: none;
}
.tl-img-212{
  width: 212rpx;
  height: 320rpx;
  margin-bottom: 54rpx;
}
.font-60-333{
  font-size: 60rpx;
  font-family: YouSheBiaoTiYuan-Regular, YouSheBiaoTiYuan;
  font-weight: 500;
  color: #333333;
}
.pcs{
  font-size: 32rpx;
  font-family: AlibabaPuHuiTi, AlibabaPuHuiTi;
  font-weight: bold;
  color: #CCCCCC;
}
.tl-font-24-999{
  font-size: 24rpx;
  font-family: Source Han Sans SC-Normal, Source Han Sans SC;
  font-weight: 400;
  color: #999999;
}
.tl-font-28-999{
  font-size: 28rpx;
  font-family: Source Han Sans SC-Medium, Source Han Sans SC;
  font-weight: 500;
  color: #999999;
}
.mt-356{
  margin-top: 356rpx;
  margin-bottom: 12rpx;
}
.tl-img-28 {
  width: 28rpx;
  height: 28rpx;
  margin-top: -6rpx;
  vertical-align: middle;
}
.u-btn--default {
  color: #E8C12F !important;
}
.tl-text-markers {
  color: #1ACDE8 !important;
}
</style>
