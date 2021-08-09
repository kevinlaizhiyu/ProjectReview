#
# 概要
【需求场景】
node层进行一个时间判断，并返回相应的时间标记

# 1. 第三方工具库的引入

```js
const moment = require("moment");
const cache = require("@zbj/utopia-cache").getInstance(); //用于读写redis储存
const periodSign = "cs-jie202188-period-sign";
const { decode } = require("../../lib/utils");
```
# 2. node层执行逻辑
> 1. 获取 `periodCache` 区间缓存
> 2. 获取节日设置的时间节点 `jieTimeNode`
> 3. 获取当前的时间 `serverTime`
> 4. 计算当前的时间区间 `currentPeriod` `0：未开始，1：预热期，2：活动期，3：返场期，4：已结束`
> 5. 设置区间缓存
> 6. 判断是否需要CMS缓存
> 7. 请求CMS数据 
```js
UA.onGet("/88jie/2021", async (req, res, next) => {
    const periodCache = await cache.get(periodSign).catch(() => {return null});

    const jieTimeNode = getJieTimeNode(req.query.timenode);
    const serverTime = getServerTime(req.query.day);
    const currentPeriod = getCurrentPeriod(jieTimeNode, serverTime);

    setPeriodCache(currentPeriod, periodCache);
    const needCmsCache = isNeedCmsCache(currentPeriod, periodCache);
    const cmsInfo = await getCmsFloorData(needCmsCache);

    Object.assign(res.locals, {
        currentPeriod, // 0，1，2，3，4
        cmsInfo,
        serverTime,// 以毫秒为单位的Unix时间戳
        jieTimeNode, // 配置的时间节点数组
        showZbjJieNav: UA.config.showZbjJieNav,
    });
    next();
});
```

# 3. 数据处理方法
> 获取当前的时间节点
> - 如果当前链接中有相应的时间节点参数，并且不属于开发环境时，对时间节点进行处理返回数组
> - 否则返回 `config` 文件中配置的时间节点
```js
function getJieTimeNode(timenode) {
    // 供测试模拟jieTimeNode202188
    if (timenode && UA.config.runtime !== 'product') {
        return String(timenode).split(',') || UA.config.jieTimeNode202188 || [];
    }
    // "jieTimeNode202188": ["2021-07-19", "2021-08-06", "2021-08-09", "2021-09-01"] // 每个节点开始的日期
    return UA.config.jieTimeNode202188 || [];
    
}
```

> 获取当前的活动时间
> - 如果这个时间存在并且不是开发环境，返回这个时间对应的 Unix 时间戳；
> - 否则返回  以毫秒为单位的Unix时间戳
```js
function getServerTime (day) {
    // 供测试模拟serverTime
    if (day && UA.config.runtime !== "product") {
        const val = moment(day).valueOf();
        return Number.isNaN(val) ? moment().valueOf() : val;
    }
    return moment().valueOf();
}
```
> 判断 `periodCache`是否存在 并且是否等于 `currentPeriod`，存在即为真，否则为假
```js
// 是否需要请求cms缓存
const isNeedCmsCache = (currentPeriod, periodCache) => {
  let needCmsCache = true;
  if (periodCache) {
    periodCache = JSON.parse(periodCache);
    needCmsCache = periodCache === currentPeriod;
  }
  return needCmsCache;
};

// 设置期间状态缓存
// periodCache 存在且不等于 currentPeriod 时候，设置 periodSign 字段的值为currentPeriod
// 不存在的时候也设置 periodSign 字段的值为currentPeriod
const setPeriodCache = (currentPeriod, periodCache) => {
  if (periodCache) {
    periodCache = JSON.parse(periodCache);
    if (periodCache !== currentPeriod) {
      cache.set(periodSign, currentPeriod, "EX", 80000);
    }
  } else {
    cache.set(periodSign, currentPeriod, "EX", 80000);
  }
};

```
> 这个就比较重要了
> - 入参时间节点数组，以及当前的时间
> - [moment().isBetween() 对应方法参考](http://momentjs.cn/docs/query/is-between.html)
```js
// 获取88节所处活动期间
function getCurrentPeriod(jieTimeNode = [], serverTime) {
    const serverTimeMoment = moment(serverTime);

    // 0：未开始，1：预热期，2：活动期，3：返场期，4：已结束
    if (serverTimeMoment.isBefore(jieTimeNode[0])) {
        return 0;
    } else if (serverTimeMoment.isBetween(jieTimeNode[0], jieTimeNode[1], null, '[)')) {
        return 1;
    } else if (serverTimeMoment.isBetween(jieTimeNode[1], jieTimeNode[2], null, '[)')) {
        return 2;
    } else if (serverTimeMoment.isBetween(jieTimeNode[2], jieTimeNode[3], null, '[)')) {
        return 3;
    } else {
        return 4;
    }
}

```

# 4. 服务端数据挂载全局

> 将处理后的数据返回到前台，并设置成全局状态 \
> 在全局状态中 使用计算属性 `getter` 生成三个值 `notStart` `isStart` `isEnd`
```js
// app>nuxt>pages>88jie>2021>index.vue
  async asyncData({ res, store }) {
    const {
      cmsInfo = {},
      showZbjJieNav,
      jieTimeNode = [], // 时间节点数组
      currentPeriod, // 当前的时间区间 0，1，2，3，4
      serverTime // 地址栏传递的服务时间
    } = res.locals

    store.commit('jie202188/setJieTimeNode', jieTimeNode)
    store.commit('jie202188/setPeriod', currentPeriod)
    store.commit('jie202188/setServerTime', serverTime)

    return {
      showZbjJieNav,
      ...cmsInfo
    }
  },
```
```js
export const state = function () {
  return {
    jieTimeNode: [],
    period: 0,
    serverTime: 0,
  };
};

export const mutations = {
  setJieTimeNode(state, payload) {
    state.jieTimeNode = payload;
  },
  setPeriod(state, payload) {
    state.period = payload;
  },
  setServerTime(state, payload) {
    state.serverTime = payload;
  },
};

export const getters = {
  notStart(state) {
    return state.period === 0;
  },
  isStart(state) {
    return state.period > 0 && state.period < 4;
  },
  isEnd(state) {
    return state.period === 4;
  },
};
```
# 5. 页面调用时间节点
```vue
<script>
import { mapState,mapGetters } from 'vuex';
export default {
  computed:{
    ...mapGetters("jie202188", ["notStart", "isStart", "isEnd"]),
  },
  methods:{
    couponsState(type='getCoupon'){
      switch (true) {
        case this.notStart:
          return{ style:'gray', text:'未开始', disable:false}
        case this.isStart:
          if(type==='getCoupon'){
            if(this.isLogin){
              return this.couponBtnStatus.code === 1?{ style:'gray', text:'已领取', disable:false}:{ style:'normal', text:'一键领取礼包', disable:true}
            }else{
              return { style:'normal', text:'一键领取礼包',disable:true}
            }
          }
          if(type==='checkRewards'){
            return{ style:'normal', text:'查看奖品', disable:true}
          }
        case this.isEnd:
          return{ style:'gray', text:'已结束', disable:false}
        default:
          return{ style:'', text:'-', disable:false}
      }
    },
  
 
  },
}
</script>

```
