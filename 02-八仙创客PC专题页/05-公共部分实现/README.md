# 
# 1.公共弹窗

* store 状态设置，控制弹窗的展示、关闭，设置回调函数；

`store > maker.js `
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
* 弹窗组件封装、挂载；
```vue
<template>
  <div :class="`page-maker-dialog ${showCommonDialog ? 'active' : ''}`">
    <div class="mask"></div>
    <div class="dialog-popup">
      <i class="icon-close close" @click="close"></i>
      <div class="maker-dialog-body">
        <div class="maker-dialog-body__title">
          {{ commonDialogData.mainTitle || '成为八仙创客合伙人' }}
        </div>
        <div class="maker-dialog-body_sub_title">{{
          commonDialogData.subTitle || '成为八仙创客合伙人'
          }}</div>
        <div
            :class="`maker-dialog-body__input-box ${
            isLogin && !!getUserPhone ? 'disabled' : ''
          }`"
        >
          <i class="prefix icon-telephone2"></i>
          <input
              type="text"
              maxlength="11"
              :disabled="isLogin && !!getUserPhone"
              placeholder="请输入您的手机号，仅官方可见"
              v-model="userPhone"
              class="input-phone"
          />
          <img class="suffix safe-pic" src="~/assets/img/safe.gif" />
        </div>

        <private-policy
            :isPolicyChecked="isPolicyChecked"
            @change="changePolicyChecked"
        ></private-policy>

        <div type="button" class="maker-dialog-body__btn" @click="handleCb()">
          {{ commonDialogData.btn || '提交申请' }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// eslint-disable-next-line
import { mapState, mapGetters } from 'vuex'
import privatePolicy from '@zbj/caishui-sub-pc/src/private-policy/private-policy.vue';

export default {
  components: {
    privatePolicy,
  },
  props: [],
  data() {
    return {
      isPolicyChecked: false,
      userPhone: this.$store.getters.getUserPhone || '',
    };
  },
  computed: {
    ...mapState({
      showCommonDialog: state => state.maker.showCommonDialog,
      commonDialogData: state => state.maker.commonDialogData,
    }),
    ...mapGetters(['isLogin', 'getUserPhone']),
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
        vm.$message.warning('请输入手机号码');
        return;
      } else if (!vm.phoneValidate(vm.userPhone)) {
        vm.$message.warning('请输入正确的手机号码');
        return;
      } else if (!vm.$store.state.userPolicyChecked && !vm.isPolicyChecked) {
        vm.$message.warning('请阅读并勾选猪八戒网《隐私保护政策》');
        return;
      }

      if (vm.commonDialogData.cb) {
        vm.commonDialogData.cb(vm.userPhone);
      }
    },
  },
  mounted() {},
};
</script>

<style lang="less" scoped>
.page-maker-dialog {
  z-index: 1999;
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
    width: 100%;
    height: 100%;
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
    width: 368px;
    height: 270px;
    background: #fff;
    transition: all 300ms;
    transform: translate(-50%, -50%) scale(0);
    transform-origin: center center;
    overflow: hidden;
    .close {
      cursor: pointer;
      z-index: 2;
      position: absolute;
      right: 10px;
      top: 10px;
      display: block;
      font-size: 22px;
      color: #777;
      transform-origin: center center;
      transition: all 200ms;
      &:hover {
        transform: rotate(90deg);
      }
    }
    .maker-dialog-body {
      width: 100%;
      height: 100%;
      overflow: hidden;
      padding: 40px 26px 0px 26px;
      box-sizing: border-box;
      .maker-dialog-body__title {
        font-size: 20px;
        text-align: left;
        font-weight: 400;
        color: #333;
      }
      .maker-dialog-body_sub_title{
        font-size: 14px;
        text-align: left;
        font-weight: 400;
        color: #999;
        margin-bottom: 22px;
      }
      .maker-dialog-body__input-box {
        height: 42px;
        line-height: 42px;
        background-color: #fff;
        border: 1px solid #ccc;
        border-radius: 2px;
        display: flex;
        justify-content: space-between;
        flex-wrap: nowrap;
        align-items: center;
        padding: 0;
        margin-bottom: 10px;
        &.disabled {
          background-color: #ececec;
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
          line-height: 16px;
          font-size: 12px;
          color: #333;
          border: none;
          padding: 0;
          margin: 0;
          &::-webkit-input-placeholder {
            color: #b4bccc;
          }
          &:disabled {
            background-color: transparent;
          }
        }
        .suffix {
          height: 38px;
          margin: 0px 6px 0px 10px;
        }
      }
      /deep/ .zbj-private-policy-agreed {
        padding: 0;
        color: #339900;
      }
      .zbj-private-policy-checkbox {
        text-align: center;
        .policy-txt {
          color: #777;
          .goto-policy {
            color: #ff6600;
          }
        }
      }
      .maker-dialog-body__btn {
        cursor: pointer;
        height: 40px;
        background: #ff6900;
        border-radius: 2px;
        line-height: 40px;
        font-size: 16px;
        color: #fff;
        text-align: center;
        padding: 0;
        margin-top: 18px;
        border: none;
      }
    }
  }
}
</style>

```  

* 在 index.vue 父组件中，设置商机提交回调方法，并设置开启弹窗动作；  
* 弹窗提交商机校验通过之后，执行关闭弹窗的动作；

# 2.商机提交

* 在 index.vue 父组件中，设置商机提交回调方法，给定参数；
* 在交互页面触发更改全局状态的方法，调出弹窗，设置回调；
* 弹窗提交商机校验；
