# 
# 1. PC 快速注册登录组件的注意事项

快速登录注册组件分为两种，一种是直接使用的快捷模板，一种是自定义模板

## 快捷模板
- 保证 `sea.js` 的安装
- 确定使用的版本 `0.1.6`
- 创建一个 `id="login-wrap"` 的空元素，用于放置快速登录注册组件
- `wayType` 是集团给每个引用组件的业务分配的渠道 财税是38；`intention`同上，财税为1

```js
    installQuickLogin() {
      let vm = this
      seajs.use('fe-common/utopia-quicklogin@0.1.6', QuickLogin => {
        vm.loginComp = new QuickLogin({
          element: '#login-wrap',
        });

        try {
          document.querySelector(".employ-dialog-modifyphone-input[name='modifyphone-yzm']").setAttribute('placeholder', '请输入验证码')
        } catch (err) {
          console.log(err)
        }

        $('#login-wrap').append(`
        <input type="hidden" name="way_type" value="${this.wayType}">
        <input type="hidden" name="user_intention" value="${this.intention}">`);
        //给按钮添加登录功能
        $('.login-btn').on('click', () => {
          var inputValue = document.querySelector(".employ-dialog-modifyphone-input[name='modifyphone']")
          vm.userPhone=inputValue.value&&inputValue.value.length<12?inputValue.value:inputValue.value.slice(0,11); //获取电话号码，优化的时候添加此代码
          vm.handleValidation() // 验证函数
        });
        vm.loginComp.on('logincomplete', info => {
          const verifyRes = info && info.length && info[0] || {};
          if (verifyRes && !verifyRes.success) {
            if (verifyRes.msg === '系统内部错误') {
              setTimeout(() => {
                document.querySelector('.j-verify-error .j-error-tip').innerHTML = '请输入正确的验证码';
              }, 0)
            }
          }
        });
      });
    }
```
# 2. Mobile快速登录注册组件的注意事项

# 3. 快速登录组件样式优化

# 4. 快速登录注册组件内部提示信息处理

# 5. 快速登录原理 以及 Chrome 浏览器不支持 http 环境写入 cookie
