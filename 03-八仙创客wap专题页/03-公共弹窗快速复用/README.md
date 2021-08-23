# 

# 1. 公共弹窗的组成

* `store` 设置全局状态
* 提交手机号的弹窗组件封装
* 公共弹窗挂载
* 交互页面调用弹窗
* 弹窗回调执行之后的弹窗的关闭

# 2. store设置全局状态
手机号弹窗、提交成功弹窗、商机回调函数设置
* 局部 `store` 设置
* 局部 `state` 三个
* 局部 `mutation` 四个

`store > maker.js`
```js
export const state = function () {
  return {
    showCommonDialog: false,
    showSuccessDialog: false,
    commonDialogData: {},
  };
};
export const mutations = {
  setShowCommonDialog(state, payload) {
    state.showCommonDialog = payload;
  },
  setShowSuccessDialog(state, payload) {
    state.showSuccessDialog = payload;
  },
  setCommonDialogData(state, payload) {
    state.commonDialogData = payload;
  },
  commonToSuccess(state, payload) {
    state.showCommonDialog = false;
    state.showSuccessDialog = true;
    state.commonDialogData = {};
  },
};
```
# 3. 提交手机号的弹窗组件封装
* 展示状态绑定
* 手机输入样式
* 隐私协议组件引入
* 提交按钮样式
* 手机号函数校验
* 全局回调函数获取和执行
```vue
<template>
  <div :class="`page-sales-dialog ${showCommonDialog ? 'active' : ''}`">
    <div class="mask" @click="close" @touchmove.prevent></div>
    <div class="dialog-popup" @touchmove.prevent>
      <i class="close icon-close" @click="close" />
      <div class="sales-dialog-body">
        <div class="sales-dialog-body__title">
          {{ commonDialogData.mainTitle || '成为八仙创客合伙人' }}
        </div>
        <div class="sales-dialog-body_sub_title">
          {{ commonDialogData.subTitle || '成为八仙创客合伙人' }}
        </div>
        <div
          :class="`sales-dialog-body__input-box ${
            isLogin && getUserPhone ? 'disabled' : ''
          }`"
        >
          <i class="prefix icon-telephone2"></i>
          <input
            type="text"
            maxlength="11"
            :disabled="isLogin && getUserPhone"
            placeholder="请输入您的手机号，仅官方可见"
            v-model="userPhone"
            class="input-phone"
          />
          <img class="suffix safe-pic" src="~/assets/img/safe.gif" />
        </div>

        <private-policy
          :isPolicyChecked="isPolicyChecked"
          @change="changePolicyChecked"
          class="private-policy"
        ></private-policy>

        <mt-button class="sales-dialog-body__btn" @click="handleCb()">
          {{ commonDialogData.btn || '提交申请' }}
        </mt-button>
      </div>
    </div>
  </div>
</template>

<script>
// eslint-disable-next-line
import { mapState, mapGetters } from 'vuex';
import privatePolicy from '~/components/common/private-policy/private-policy.vue';

export default {
  components: {
    privatePolicy
  },
  props: [],
  data() {
    return {
      isPolicyChecked: false,
      userPhone: this.$store.getters.getUserPhone || ''
    };
  },
  computed: {
    ...mapState({
      showCommonDialog: (state) => state.maker.showCommonDialog,
      commonDialogData: (state) => state.maker.commonDialogData
    }),
    ...mapGetters(['isLogin', 'getUserPhone'])
  },
  methods: {
    close() {
      this.$store.commit('maker/setCommonDialogData', {});
      this.$store.commit('maker/setShowCommonDialog', false);
    },
    changePolicyChecked(status) {
      this.isPolicyChecked = status;
    },
    handleCb() {
      const vm = this;
      if (!vm.userPhone) {
        vm.$toast('请输入手机号码');
        return;
      } else if (!vm.phoneValidate(vm.userPhone)) {
        vm.$toast('请输入正确的手机号码');
        return;
      } else if (!vm.$store.state.userPolicyChecked && !vm.isPolicyChecked) {
        vm.$toast('请阅读并勾选猪八戒网《隐私保护政策》');
        return;
      }

      if (vm.commonDialogData.cb) {
        vm.commonDialogData.cb(vm.userPhone);
      }
    }
  },
  mounted() {}
};
</script>

<style lang="less">
.page-sales-dialog {
  z-index: 1000;
  position: fixed;
  width: 100vw;
  height: 100vh;
  top: 0;
  left: 0;
  visibility: hidden;
  &.active {
    visibility: visible;
    .dialog-popup {
      visibility: visible;
      transform: translate(-50%, -50%) scale(1);
    }
  }
  .mask {
    z-index: 1;
    position: absolute;
    width: 100vw;
    height: 100vh;
    top: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.5);
  }
  .dialog-popup {
    z-index: 2;
    visibility: hidden;
    position: absolute;
    top: 50%;
    left: 50%;
    width: 82vw;
    height: auto;
    background-color: #fff;
    transition: all 300ms;
    transform: translate(-50%, -50%) scale(0);
    transform-origin: center center;
    overflow: hidden;
    border-radius: 0.8vw;
    .close {
      z-index: 2;
      position: absolute;
      right: 3vw;
      top: 3vw;
      display: block;
      font-size: 6vw;
      color: #949494;
    }
    .sales-dialog-body {
      width: 100%;
      height: 100%;
      overflow: hidden;
      padding: 8.1vw 6.4vw 9.4vw;
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      align-items: center;
      .sales-dialog-body__title {
        //letter-spacing: 1px;
        width: 100%;
        height: auto;
        text-align: left;
        font-size: 4.8vw;
        font-weight: bold;
        color: #333;
      }
      .sales-dialog-body_sub_title {
        //letter-spacing: 1px;
        width: 100%;
        height: auto;
        text-align: left;
        font-size: 12px;
        font-weight: 400;
        color: #999;
        margin-bottom: 3.6vw;
      }
      .sales-dialog-body__input-box {
        width: 100%;
        height: 40px;
        line-height: 16px;
        background-color: #fff;
        display: flex;
        justify-content: space-between;
        flex-wrap: nowrap;
        align-items: center;
        padding: 0;
        border: 1px solid #c2c2d4;
        border-radius: 4px;
        //margin-bottom: 1.8vw;
        &.disabled {
          background-color: #ececec;
          input {
            background-color: #ececec;
          }
        }
        .input-phone {
          height: 16px;
          font-size: 12px;
          background: #fff;
          color: #999;
        }
        .prefix {
          font-size: 18px;
          height: 28px;
          line-height: 28px;
          color: #c8ced9;
          margin: 0 10px 0 8px;
        }
        input {
          flex: 1;
          font-size: 12px;
          color: #999;
          border: none;
          padding: 0;
          margin: 0;
          &::-webkit-input-placeholder {
            color: #999;
          }
        }
        .suffix {
          height: 28px;
          margin: 0px 6px 0px 10px;
        }
      }
      .private-policy {
        margin-top: 0;
        padding: 10px 0 15px 0; // 控制弹窗勾选的位置
        width: 110%;
        .zbj-private-policy-checkbox {
          .el-checkbox__input {
            .el-checkbox__inner {
              background-color: #fafcfd;
            }
          }
          .el-checkbox__label {
            span {
              color: #999;
              a {
                color: #ff6900;
              }
            }
          }
          &.policy-checked {
            padding: 2px 0px;
          }
        }
      }
      .sales-dialog-body__btn {
        width: 100%;
        height: 9vw;
        line-height: 9vw;
        border-radius: 2px;
        background: #ff6900;
        font-size: 14px;
        color: #fff;
        text-align: center;
        padding: 0;
        margin-top: 0vw; //2vw
        border: none;
      }
    }
  }
}
</style>

```
# 4. 公共弹窗挂载
挂载并注册在 `index.vue` 中

# 5. 交互页面调用弹窗
商机提交函数也要写在 `index.vue` 中，通过自定义事件传递到组件内部

组件内部定义好参数，用户点击之后，通过 `this.$emit('methodsName',params1,params2)` 执行

`page > zt > maker > index.vue`
```vue
<template>
  <div>
    <makerTopBanner :topBannerInfos='topBannerInfos' />
    <makerMiddleBanner :middleBannerInfos='middleBannerInfos'/>
    <makerRights @openCommonDialog='openCommonDialog'/>
    <makerPartner @openCommonDialog='openCommonDialog' />
    <makerProducts @openCommonDialog='openCommonDialog'/>
    <successDialog :alertData='alertData'/>
  </div>
</template>

<script>
import makerTopBanner from '~/components/maker/maker-topbanner';
import makerMiddleBanner from '~/components/maker/maker-middle-banner';
import makerRights from '~/components/maker/maker-rights';
import makerPartner from '~/components/maker/maker-partner';
import makerProducts from '~/components/maker/maker-products';
import successDialog from '~/components/maker/scuccess-dialog';

export default {
  components: {
    makerTopBanner,
    makerMiddleBanner,
    makerRights,
    makerPartner,
    makerProducts,
    successDialog
  },
  async asyncData({ res }) {
    const { cmsInfo = {} } = res.locals;
    return {
      ...cmsInfo,
    };
  },
  methods:{
    openCommonDialog(dialogInfos = {}, chanceParams = {}) {
      const vm = this;
      const { mainTitle = '', subTitle = '', btnText = '' } = dialogInfos;
      this.$store.commit('maker/setCommonDialogData', {
        mainTitle,
        subTitle,
        btnText,
        cb: (userPhone = '') => {
          const params = {
            userPhone,
            ...chanceParams,
          };
          vm.submitChanceAndHistory({ params })
            .then(({ data }) => {
              if (data.success) {
                vm.$store.commit('maker/commonToSuccess');
              } else {
                vm.$toast(data.description);
              }
            })
            .catch((err) => {
              vm.$toast('提交失败，请重试');
            });
        },
      });
      this.$store.commit('maker/setShowCommonDialog', true);
    },
  },
};
</script>


```
