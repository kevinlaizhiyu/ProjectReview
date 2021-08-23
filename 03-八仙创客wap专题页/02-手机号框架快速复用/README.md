# 

# 1.手机号验证框架组成

* 输入框 手机图标 安全图标
* 隐私协议组件 
* 提交按钮
* 手机号隐私协议校验函数
* 商机提交函数

# 2. 输入框的快速复用
* 输入框dom 元素和 css 结构模板化
* 引入 mapGetters 获取全局登录状态和电话号码
* data 中对全局登录状态和电话号码进行初始化配置
* v-model 状态到输入框 控制 disable value
* css 样式的修改

# 3. 隐私协议的快速复用
* 引入隐私协议
* 注册隐私协议
* 配置隐私协议勾选状态，传递该状态到组件
* 设置隐私协议勾选方法，传递方法到组件内部
* 设置隐私协议颜色字体样式，/deep/ 进行样式穿透

# 3. 提交验证的快速复用
* 设置用户校验函数
* 函数中对电话号码、是否勾选隐私协议进行判断，并弹出提示
* 配置商机提交的参数
* 调用全局的商机提交的方法，并设置成功/失败的函数提示

```vue
<template>
  <div class="maker_join_wrap">
    <div class="maker_join_center">
      <!-- 标题 -->
      <div class="maker_join_main_title">加入申请</div>
      <div :class="`maker_input_box ${isLogin && getUserPhone ? 'disabled' : ''}`">
        <!-- 手机图标 -->
        <i class="prefix icon-telephone2"></i>
        <!-- 输入框 禁用样式 -->
        <input
            type="text"
            maxlength="11"
            :disabled="isLogin && getUserPhone"
            placeholder="请输入您的手机号"
            v-model="userPhone"
            class="input_phone"
        />
        <!-- 安全图标 -->
        <img class="suffix safe-pic" src="~/assets/img/safe.gif" />
      </div>
      <!-- 隐私协议 -->
      <privatePolicy
          :isPolicyChecked="isPolicyChecked"
          @change="changePolicyChecked"
          class="private_policy"
      ></privatePolicy>
      <!-- 提交按钮 -->
      <div class="maker_join_button" @click="handleCb">提交申请</div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';
// 引入隐私协议组件
import privatePolicy from '~/components/common/private-policy/private-policy.vue';
export default {
  components: {
    privatePolicy //注册
  },
  computed: {
    ...mapGetters(['isLogin', 'getUserPhone'])//获取 登录状态及手机号码
  },
  data() {
    return {
      isPolicyChecked: false, //初始化配置隐私协议勾选状态
      userPhone: this.$store.getters.getUserPhone || '' //初始化配置用户的手机号
    };
  },
  methods: {
    //勾选函数
    changePolicyChecked(status) {
      this.isPolicyChecked = status;
    },
    //校验函数
    handleCb() {
      const vm = this;
      //校验功能
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
      //参数设置
      const params = {
        userPhone: vm.userPhone,
        chanceTypeId: '100104',
        requestType: '155',
        remark: 'WAP-八仙创客专题：申请成为八仙创客合伙人'
      };
      //商机提交 
      vm.submitChanceAndHistory({ params })
          .then(({ data }) => {
            if (data.success) {
              vm.$store.commit('maker/commonToSuccess');//toast轻提示
            } else {
              vm.$toast(data.description);
            }
          })
          .catch((err) => {
            vm.$toast('提交失败，请重试');
          });
    }
  }
};
</script>

<style lang='less' scoped>
.maker_join_wrap {
  width: 100%;
  height: 233px;
  position: relative;
  .maker_join_center {
    width: 343px;
    height: 220px;
    background: #fff;
    position: absolute;
    top: -18px;
    left: 50%;
    transform: translateX(-50%);
    z-index: 1;
    box-shadow: 0px 6px 46px 0px rgba(196, 198, 199, 0.43);
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    .maker_join_main_title {
      font-size: 20px;
      font-weight: bold;
      font-style: italic;
      color: #333333;
      position: relative;
      margin-top: 25px;
      &::before {
        display: block;
        position: absolute;
        content: '';
        width: 9px;
        height: 1px;
        background: #333;
        right: -21px;
        top: 50%;
        transform: translateY(-50%);
      }
      &::after {
        display: block;
        position: absolute;
        content: '';
        width: 9px;
        height: 1px;
        background: #333;
        left: -21px;
        top: 50%;
        transform: translateY(-50%);
      }
    }
    .maker_input_box {
      width: 307px;
      height: 44px;
      background-color: #f3f3f3;
      display: flex;
      justify-content: space-between;
      flex-wrap: nowrap;
      align-items: center;
      padding: 0;
      border-radius: 4px;
      margin-top: 17px;
      &.disabled {
        background-color: #ececec;
        input {
          background-color: #ececec;
        }
      }
      .input_phone {
        height: 16px;
        font-size: 12px;
        background: #f3f3f3;
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
        font-size: 13px;
        color: #999;
        border: none;
        padding: 0;
        margin: 0;
        &::-webkit-input-placeholder {
          color: #999;
        }
      }
      .suffix {
        height: 38px;
        margin: 0px 6px 0px 10px;
      }
    }
    .private_policy {
      margin-top: 0;
      padding: 4px 0 12px 20px; // 控制弹窗勾选的位置
      width: 100%;
      /deep/.zbj-private-policy-checkbox {
        text-align: left;
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
    .maker_join_button {
      width: 307px;
      height: 44px;
      display: flex;
      justify-content: center;
      align-items: center;
      background: linear-gradient(90deg, #ff6900 0%, #fe9600 100%);
      border-radius: 6px;
      font-size: 13px;
      font-weight: 400;
      color: #ffffff;
    }
  }
}
</style>

```
