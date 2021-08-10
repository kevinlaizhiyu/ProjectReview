# 

# 概要
- PC端注意引入 `seajs`，样式优化，
- WAP注意要引入` utopia-core `以及 `seajs` 
- 版本问题永远是要关注的点，目前` 0.1.6 `是比较稳定的版本
- 样式优化比较繁琐，需要再浏览器中仔细查看相应的 `class` 属性
- 集团系统信息处理需要进行手动过滤，在生命周期中实现
- 修改默认样式文案，需要对 `dom` 进行修复

# 1. PC 快速注册登录组件的注意事项

快速登录注册组件分为两种，一种是直接使用的快捷模板，一种是自定义模板(该用法之后再补上)

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
- `zbjInfo` 是 `node` 层处理好的一些数据，封装在一个对象中返回的，必要信息
- 引入 `utopia-core 0.0.2` 以及 `seajs`

其余部分和PC的使用方式没有什么不同
```js
    initQuickLogin() {
      window.ZBJInfo = this.zbjInfo;
      const me = this;
      const script = document.createElement('script');
      script.src =
        `${window.location.protocol}//as.zbjimg.com/rake-component/fe-common/utopia-core/0.0.2/index.js`;
      script.onload = () => {
        seajs.use("fe-common/utopia-quicklogin@0.1.6", (QuickLogin) => {
          me.loginComp = new QuickLogin({
            element: '#qloginbar_container',
          });
          // 组件处理
          $('#qloginbar_container').append('<input type="hidden" name="way_type" value="38"><input type="hidden" name="user_intention" value="1">');
          $("input[name='modifyphone']").val(me.userPhone);
          $('.employ-dialog-modifyphone-verify').removeClass('disabled');
          $('.employ-dialog-modifyphone-verify').removeAttr('disabled');
          try {
            document.querySelector(".employ-dialog-modifyphone-input[name='modifyphone-yzm']").setAttribute('placeholder', '请输入验证码')
          } catch (err) {
            console.log(err)
          }
          //给按钮添加登录功能
          $('.login-btn').on('click', () => {
            var inputValue = document.querySelector(".employ-dialog-modifyphone-input[name='modifyphone']")
            me.userPhone=inputValue.value&&inputValue.value.length<12?inputValue.value:inputValue.value.slice(0,11);
            me.handleValidation()
          });

          me.loginComp.on('logincomplete', info => {
            console.log("logincomplete", info)
            const verifyRes = info && info.length && info[0] || {};
            if (verifyRes && !verifyRes.success) {
              if (verifyRes.msg === '系统内部错误') {
                setTimeout(() => {
                  document.querySelector('.j-verify-error .j-error-tip').innerHTML = '请输入正确的验证码';
                }, 0)
              }
            }
          });
        })
      };

      document.body.appendChild(script);
    },
```
# 3. 快速登录组件样式优化
集团快捷登录组件默认样式比较简单，组件样式优化的思路是，在 `style` 文件中覆盖该组件元素的样式即可

- 修改组件的样式，要么就去掉 `scoped` 要么就使用 `/deep/` 样式穿透
- 某些样式需要 `!important`

```css
#login-wrap{
    width: 332px;
//组件默认标题不显示
  .employ-dialog-section-title{
      display: none
  }
.employ-dialog-modifyphone{
    width: 100%;
    height: 115px;
    margin-bottom: 21px;
.employ-dialog-modifyphone-inputwrap{
    width: 100%;
    margin-bottom: 20px;
//手机号输入框样式设置
  .employ-dialog-modifyphone-input{
      width: 100%;
      height: 50px;
      border-radius: 4px;
      border: 1px solid #DCDCDC;
      font-size: 18px;
      font-weight: 400;
      color: #999999;
      line-height: 8px;
      text-indent: 32px;
      background: #fff !important;
&::-webkit-input-placeholder {
     font-size: 16px;
     font-weight: 400;
     color: #999999;
 }
}
//手机号提示信息不显示
  .modifyphone-error {
      height: 0px;
      margin: 0;
      padding: 0;
      border: none;
.iconfont{ //错误的icon不显示
display:none !important;
}
}
}
//验证码部分
  div:nth-child(2){
      width: 332px;
      height: 50px;
      border-radius: 4px;
      border: 1px solid #DCDCDC;

.employ-dialog-modifyphone-input{
    width: 210px;
    height: 48px;
    border: none;
    font-size: 16px;
    font-weight: 400;
    color: #999999;
    line-height: 8px;
    text-indent: 12px;
    margin: 0;
    float: left;
    border-radius: 3px;
&::-webkit-input-placeholder {
     font-size: 16px;
     font-weight: 400;
     color: #999999;
 }
//手机号提示信息不显示
  .modifyphone-error {
      display: none !important;
  }
}
.employ-dialog-modifyphone-verify{
    width: 118px;
    height: 48px;
    border: 0;
    margin: 0;
    background: #F21717 !important;
    padding: 0;
    float: right;
    font-size: 14px;
    font-weight: 400;
    color: #fff;
    line-height: 48px;
    border-top-left-radius: 0px;
    border-bottom-left-radius: 0px;
    border-top-right-radius: 3px;
    border-bottom-right-radius: 3px;
    text-align: center;
}
//验证码错误以及登录提示信息不显示
  .modifyphone-error{
&.j-agreement-error{
     display: none !important;
 }
&.j-verify-error{
.iconfont{ //错误的icon不显示
display:none !important;
}
}
}
}
}
}
```

# 4. 快速登录注册组件内部提示信息处理
快捷登录组件有许多的提示信息，某些提示信息可能并不是我们想要的，于是要对返回的特定信息（譬如 `error info` ） 或者组件原生展示的信息进行相应的优化。（譬如 `placeholder`）

## 过滤错误信息
```js
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
```

## 更改原生展示信息
```js
try {
  document.querySelector(".employ-dialog-modifyphone-input[name='modifyphone-yzm']").setAttribute('placeholder', '请输入验证码')
} catch (err) {
  console.log(err)
}
```


# 5. 快速登录原理 以及 Chrome 浏览器不支持 http 环境写入 cookie

需要注意的是，快捷登录操作，开发的时候在 `chrome` 预览不会生效，表现为本来应该写入 `cookie` 的 `uid` `userkey` 不存在

- 原因：`Chrome` 浏览器最新版本不再支持在 `http` 协议下写入 `cookie` 。

- 处理：\
  【开发】解决办法是还  `Safari` `Firefox` 浏览器进行快捷登录的开发联调，但未来可能这两个浏览器也会做这样的更新，所以最好就使用旧版的浏览器进行相应的开发。\
  【测试】测试环境访问使用 `https` 


