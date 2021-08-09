# 
# 概要
【应用场景】
- 点击按钮，页面出现一个弹窗
【实现思路】
- 1. 每一类弹窗封窗成一个组件挂载在根模板上
- 2. 每一个组件设置2个全局状态，`状态1`控制是否显示隐藏，`状态2`控制页面展示信息(包括 弹窗展示信息 和 弹窗按钮回调函数) 
- 3. 每一个全局状态对应一个全局方法用于更改此全局状态
  

# 1. 封装弹窗组件并挂载
  - 1. 根据`showCommonDialog`属性控制该组件是否显示
  - 2. `close()`方法 设置`showCommonDialog=false` 并清空`状态2`
  - 3. ` handleCb()`方法 执行全局状态下保存的回调函数
  - 4. 挂载`app>nuxt>pages>index.vue`上
  

```vue
<script>
export default {
  props: [],
  data() {
    return {
       
    };
  },
  computed: {
    ...mapState('jie202188',[ 'commonDialogData','showCommonDialog']),
  },
  methods: {
    close() {
      this.$store.commit("jie202188/setShowCommonDialog", false);
      this.$store.commit("jie202188/setCommonDialogData", {});
    },
   
    handleCb() {
      const vm = this;
      if (vm.commonDialogData.cb) {
        vm.commonDialogData.cb(vm.userPhone);
      }
    },
  },
  mounted() {},
};
</script>
```

# 2. 页面进行弹窗调用

> 【注意】：此处将该方法放置在pages中，哪个组件需要自行调用即可
```js
    // 商机提交
chanceHandle(arr, titleBtn, params) {
  const vm = this
  const dialogData = {
    title: titleBtn[0] || '',
    btn: titleBtn[1] || '',
    cb:
      (userPhone = '') => {
        params.phone = userPhone
        vm.$axios.get('/chance/shareChancePushLog', { params })
          .then(({ data }) => {
            console.log(data)
            if (data.success) {
              vm.$store.commit('jie202188/setShowCommonDialog', false)
              vm.$store.commit('jie202188/setShowSuccessDialog', true)
            } else {
              vm.$message.warning(data.description || '提交失败，请重试')
            }
          })
          .catch((err) => {
            vm.$message.warning('提交失败，请重试')
          })
    }  
  }
  vm.$store.commit('jie202188/setCommonDialogData', dialogData)
  vm.$store.commit('jie202188/setShowCommonDialog', true)
}
```

# 3. 全局状态设置

```js
//app>nuxt>store>jie882021.js
export const state = function () {
  return {
    showCommonDialog: false,
    showSuccessDialog: false,
    commonDialogData: {},
  };
};

export const mutations = {
  setShowCommonDialog(state, payload){
    state.showCommonDialog = payload;
  },
  setCommonDialogData(state, payload){
    state.commonDialogData = payload;
  },
  setShowSuccessDialog(state, payload){
    state.showSuccessDialog = payload;
  },
};


```
