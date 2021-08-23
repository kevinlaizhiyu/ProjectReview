#
# 1. 概述

  之所以是要手写轮播图，主要是因为很多的时候使用第三方轮播图有很多的缺点，简单的轮播图自然是都能够实现，但是你譬如说，网站首屏加载，组件渲染演示导致页面出现短暂的白屏，这个在交互中是不大被允许的。

- `elementUI` 渲染不白屏，但是功能太单调了，并且目前不支持两页的无线单向循环，两页只是往复循环，然后不能够进行拖动，只能够通过触发分页器来实现轮播图的切换，只适用于简单快速的轮播；

- `swiper` 强大的轮播图组件，通过配置选项，能够实现PC WAP 多端的适配，并且还支持分组、渐变等等诸多功能，但是会有一些小的 bug 出现导致需要一些"非常"手段进行 bug 的修复，可以适用于比较复杂的轮播图；
  
本次的需求跟财税官网的需求相似，
  
> 1. banner 进行渐变切换；
> 2. banner 背景图能进行点击；
> 3. 操作分页器能够实现 banner 的切换 

# 2. `top-banner` 组件内部

```vue
<template>
  <div class="banner_wrap">
    <!-- 轮播图部分 -->
    <div class="banner_picwrap">
      <a
        :href="nowPic.link || 'javascript:void(0)'"
        :target="nowPic.link ? '_blank' : ''"
      >
        <img
          draggable="false"
          :src="nowPic.picUrl || ''"
          :class="['now-pic', picChanging && 'pic-changing']"
        />
      </a>
    </div>
    <!-- 分页器 -->
    <div class="pics-piganition" v-show="topBannerInfos && topBannerInfos.length > 1">
      <div
        :class="['pic-pg-one', picIndex === index && 'active']"
        v-for="(item, index) in topBannerInfos"
        :key="`${selectedIndex}pic-pg-one${index}`"
        @click="roundPic(index, true)"
      ></div>
    </div>
    <!-- 申请按钮 -->
    <div class="apply_btn" @click="topBannerSubmitChance">
      <commonBtn :btnText="`立即申请成为创客合伙人`"/>
    </div>
  </div>
</template>

<script>
import commonBtn from './common-button.vue';

export default {
  components: {
    commonBtn,
  },
  props: {
    topBannerInfos: {
      type: Array,
      default: [],
    },
  },
  computed: {
    nowPic() {
      return (this.topBannerInfos.length > 0 && this.topBannerInfos[this.picIndex]) || {};
    },
  },
  data() {
    return {
      picIndex: 0,
      picChanging: false,
      _bannerPicAni: null,
      _bannerPicRound: null,
      selectedIndex: 0,
      autoplay: {
        delay: 5000,
      },
    };
  },
  mounted() {
    this.startPicRound(true);
  },
  methods: {
    topBannerSubmitChance() {
      const dialogInfos = {
        mainTitle: '申请成为八仙创客合伙人',
        subTitle: '拓宽业务范围，深挖客户价值，同创持久财富',
        btnText: '提交申请',
      };
      const chanceParams = {
        chanceTypeId: '100104',
        requestType: '155',
        remark: 'PC-八仙创客专题：申请成为八仙创客合伙人',
      };
      this.$emit('openCommonDialog', dialogInfos, chanceParams);
    },
    // 图片加载成功
    onLoadPicOk() {
      const _this = this;
      if (this._bannerPicAni) {
        clearTimeout(this._bannerPicAni);
        this._bannerPicAni = null;
      }
      _this.picChanging = true;
      this._bannerPicAni = setTimeout(() => {
        _this.picChanging = false;
      }, 300);
    },
    // 切换tab
    selectTab(index = 0) {
      const _this = this;
      new Promise((resolve, reject) => {
        _this.selectedIndex = index;
        resolve();
      }).then(() => {
        _this.startPicRound(true);
      });
    },
    /**
     * 切换图片
     * selectIndex: 选择显示的分页下标
     * isUserAction: 是否用户主动点击分页器切换
     */
    roundPic(selectIndex = 0, isUserAction = false) {
      const _this = this;
      if (isUserAction && _this.picIndex === selectIndex) {
        // 用户主动点击
        return;
      }

      _this.picIndex = selectIndex;

      if (isUserAction) {
        _this.startPicRound();
      }
      this.onLoadPicOk();
    },
    /**
     * 开始图片轮播
     * resetToZero: 是否从0开始
     */
    startPicRound(resetToZero = false) {
      const _this = this;
      if (resetToZero) {
        _this.roundPic(0);
      }

      if (this._bannerPicRound) {
        clearInterval(this._bannerPicRound);
        this._bannerPicRound = null;
      }
      const nowLength = (_this.topBannerInfos && _this.topBannerInfos.length) || 0;
      if (nowLength && nowLength > 1) {
        this._bannerPicRound = setInterval(() => {
          if (_this.picIndex + 1 >= nowLength) {
            _this.roundPic(0);
          } else {
            _this.roundPic(_this.picIndex + 1);
          }
        }, this.autoplay.delay);
      }
    },
  },
};
</script>


<style lang="less" scoped>
.banner_wrap {
  width: 100%;
  height: 628px;
  position: relative;
  .banner_picwrap {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 628px;
    .now-pic {
      display: block;
      min-width: 100%;
      height: 628px;
      position: absolute;
      top: 0;
      left: 50%;
      transform: translateX(-50%);
      transition: all 300ms;
      opacity: 1;
      &.pic-changing {
        animation: picChanging 300ms ease-in both;
      }
    }
  }
  .pics-piganition {
    position: absolute;
    bottom: 31px;
    left: 50%;
    transform: translateX(-50%);
    width: auto;
    height: 13px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-direction: row;
    z-index: 19;
    .pic-pg-one {
      box-sizing: content-box;
      cursor: pointer;
      width: 40px;
      height: 4px;
      padding: 5px 0px;
      margin: 0 2px;
      border-radius: 2px;
      background: rgba(255, 255, 255, 0.53);
      background-clip: content-box;
      transition: all 300ms ease-in;
      &:hover {
        background: rgba(255, 255, 255, 0.83);
        background-clip: content-box;
      }
      &.active {
        width: 40px;
        background: #fff;
        background-clip: content-box;
      }
    }
  }
  .apply_btn {
    width: 320px;
    height: 64px;
    position: absolute;
    bottom: 100px;
    left: 50%;
    transform: translateX(-50%);
    border-radius: 32px;
    overflow: hidden;
    cursor: pointer;
  }
}
@keyframes picChanging {
  0% {
    opacity: 0.3;
  }
  100% {
    opacity: 1;
  }
}
</style>

```

# 2.1 html 部分详解

```html
<template>
  <div class="banner_wrap">
    <!-- 轮播图部分 -->
    <div class="banner_picwrap">
      <a
        :href="nowPic.link || 'javascript:void(0)'"
        :target="nowPic.link ? '_blank' : ''"
      >
        <img
          draggable="false"
          :src="nowPic.picUrl || ''"
          :class="['now-pic', picChanging && 'pic-changing']"
        />
      </a>
    </div>
    <!-- 分页器 -->
    <div class="pics-piganition" v-show="topBannerInfos && topBannerInfos.length > 1">
      <div
        :class="['pic-pg-one', picIndex === index && 'active']"
        v-for="(item, index) in topBannerInfos"
        :key="`${selectedIndex}pic-pg-one${index}`"
        @click="roundPic(index, true)"
      ></div>
    </div>
    <!-- 申请按钮 -->
    <div class="apply_btn" @click="topBannerSubmitChance">
      <commonBtn :btnText="`立即申请成为创客合伙人`"/>
    </div>
  </div>
</template>
```

- 轮播图部分是一个 `a` 标签包裹一个 `img` 标签，然后 `img` 标签中的引用地址是变化的，标签的 `class` 是会有一个切换状态变化的。

- 分页器部分是一个 分页器的外框包裹一个分页器的具体索引元素，每一个索引元素都有一个点击事件

# 2.2 js部分详解

```js
export default {
  props: {
    topBannerInfos: {
      type: Array,
      default: [],
    },
  },
  computed: {
    nowPic() {
      return (this.topBannerInfos.length > 0 && this.topBannerInfos[this.picIndex]) || {};
    },
  },
  data() {
    return {
      picIndex: 0,
      picChanging: false,
      _bannerPicAni: null,
      _bannerPicRound: null,
      selectedIndex: 0,
      autoplay: {
        delay: 5000,
      },
    };
  },
  mounted() {
    this.startPicRound(true);
  },
  methods: {
    topBannerSubmitChance() {
      const dialogInfos = {
        mainTitle: '申请成为八仙创客合伙人',
        subTitle: '拓宽业务范围，深挖客户价值，同创持久财富',
        btnText: '提交申请',
      };
      const chanceParams = {
        chanceTypeId: '100104',
        requestType: '155',
        remark: 'PC-八仙创客专题：申请成为八仙创客合伙人',
      };
      this.$emit('openCommonDialog', dialogInfos, chanceParams);
    },
    // 图片加载成功
    onLoadPicOk() {
      const _this = this;
      if (this._bannerPicAni) {
        clearTimeout(this._bannerPicAni);
        this._bannerPicAni = null;
      }
      _this.picChanging = true;
      this._bannerPicAni = setTimeout(() => {
        _this.picChanging = false;
      }, 300);
    },
    // 切换tab
    selectTab(index = 0) {
      const _this = this;
      new Promise((resolve, reject) => {
        _this.selectedIndex = index;
        resolve();
      }).then(() => {
        _this.startPicRound(true);
      });
    },
    /**
     * 切换图片
     * selectIndex: 选择显示的分页下标
     * isUserAction: 是否用户主动点击分页器切换
     */
    roundPic(selectIndex = 0, isUserAction = false) {
      const _this = this;
      if (isUserAction && _this.picIndex === selectIndex) {
        // 用户主动点击
        return;
      }

      _this.picIndex = selectIndex;

      if (isUserAction) {
        _this.startPicRound();
      }
      this.onLoadPicOk();
    },
    /**
     * 开始图片轮播
     * resetToZero: 是否从0开始
     */
    startPicRound(resetToZero = false) {
      const _this = this;
      if (resetToZero) {
        _this.roundPic(0);
      }

      if (this._bannerPicRound) {
        clearInterval(this._bannerPicRound);
        this._bannerPicRound = null;
      }
      const nowLength = (_this.topBannerInfos && _this.topBannerInfos.length) || 0;
      if (nowLength && nowLength > 1) {
        this._bannerPicRound = setInterval(() => {
          if (_this.picIndex + 1 >= nowLength) {
            _this.roundPic(0);
          } else {
            _this.roundPic(_this.picIndex + 1);
          }
        }, this.autoplay.delay);
      }
    },
  },
};
```

## 2.2.1 props 继承轮播数据
`topBannerInfos` 为一个数组

## 2.2.2 computed 中计算当前的对应数据
`nowPic` 计算的是当前数据对应索引下的数据，在上文中用于渲染 a 标签的链接和 img 资源引用地址

## 2.2.3 data 中存放对应的索引
```js
data(){
  return {
    picIndex: 0, //当前图片对应的数据索引
    picChanging: false, //"图片是否在改变"，见文知意，用于图片切换过成功的过渡效果的判断
    _bannerPicAni: null, // 轮播图切换的动画延迟，在方法部分能够看到时间
    _bannerPicRound: null, // 轮播图切换的计时器
    selectedIndex: 0, // 用来存储当前用户点击的分页器的索引，并没有什么用
    autoplay: { // 设置自动播放的时间
      delay: 5000,
    },
  };
}
```

## 2.2.4 mounted 中进行初始化挂载

```js
mounted(){
  this.startPicRound(true);
}
```

## 2.2.5 methods 方法详解

`startPicRound`
```js
 /**
     * 开始图片轮播
     * resetToZero: 是否从0开始
     */
    startPicRound(resetToZero = false) { 
      const _this = this;
      if (resetToZero) { //如果传过来的参数是true，表示这里需要重新归零
        _this.roundPic(0);
      }

      if (this._bannerPicRound) { //判断是否存在 轮播图切换计时器
        clearInterval(this._bannerPicRound); //存在就清除这和个计时器
        this._bannerPicRound = null; //并且状态归为 null
      }
      const nowLength = (_this.topBannerInfos && _this.topBannerInfos.length) || 0; //声明数据的长度
      if (nowLength && nowLength > 1) { //如果当前的数据长度存在并且长度大于1
        this._bannerPicRound = setInterval(() => { //开启计时器
          if (_this.picIndex + 1 >= nowLength) { //如果当前索引是最后一个
            _this.roundPic(0); //切换到第一图片 
          } else {
            _this.roundPic(_this.picIndex + 1); //否则切换到下载一张图片
          }
        }, this.autoplay.delay);//这里是计时器的时间，读取配置中的变量
      }
    },
```

```js
/**
     * 切换图片
     * selectIndex: 选择显示的分页下标
     * isUserAction: 是否用户主动点击分页器切换
     */
    roundPic(selectIndex = 0, isUserAction = false) {
        //次方法接受两个参数，第一个参数是被选中的索引，默认0，表示第一个，第二个参数表示是非是用户的行为，默认false 不是用的用行为
      const _this = this;
      if (isUserAction && _this.picIndex === selectIndex) {
        // 如果用户主动点击，并且当前图片的索引和 传入的索引的值是样的话，表示用户所点击的位置就是当前的位置，所以直接结束程序
        return;
      }

      _this.picIndex = selectIndex;//否则更改 picindex 为 当前传入的selectedindex
    
      // 判断是否是用户的交互，如果是用于的交互的话，那么重新初始化这个轮播进程；原来本身就有一个计时器存在这样本身要切换，用户在点击之后，轮播图重新即使
      if (isUserAction) { 
        _this.startPicRound();
      }
      // 走到这一步，轮播图的图片加载完成
      this.onLoadPicOk();
    },
```

```js
 // 图片加载成功
    onLoadPicOk() {
      const _this = this; 
      if (this._bannerPicAni) { // 判断如果这个'轮播图动画计时器'存在的话
        clearTimeout(this._bannerPicAni);// 结束这个计时器，
        this._bannerPicAni = null;//并重新将其设置为null
      }
      _this.picChanging = true; //上一步的检测之后，当前设置图片正在更改，状态打开
      this._bannerPicAni = setTimeout(() => {//设置'轮播图动画计时器'
        _this.picChanging = false; //300ms之后更改，状态关闭
      }, 300);
    },
    
```


```js
  // 切换tab
  selectTab(index = 0) {  // 传入一个索引的值
    const _this = this;
    new Promise((resolve, reject) => { // 新建一个promise 将vue 中的异步 数据更改变成同步更改
      _this.selectedIndex = index; // 在其中将 selectedindex 改成 传入的值
      resolve();
    }).then(() => {
      _this.startPicRound(true); //更改之后再重启这个 轮播进程
    });
  }
```

# 2.3 css部分重点分析

`.now-pic` 中采用绝对定位是为保证页面在缩放时，背景图片不会被压缩

```less
.now-pic {
    display: block;
    min-width: 100%;
    height: 628px;
    position: absolute;
    top: 0;
    left: 50%;
    transform: translateX(-50%);
    transition: all 300ms;
    opacity: 1;
    &.pic-changing {
      animation: picChanging 300ms ease-in both;
    }
  }
```
`.pic-changing` 添加该属性的时候，执行动画
```less
 &.pic-changing {
    animation: picChanging 300ms ease-in both;
  }
@keyframes picChanging {
  0% {
    opacity: 0.3;
  }
  100% {
    opacity: 1;
  }
}
```
