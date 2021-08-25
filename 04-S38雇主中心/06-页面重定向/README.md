#

# 1. 项目中公共链接的配置以及配置中心环境的配置

## 1.1 公共连接的配置

适用场景

node 层的 route 文件中，项目的重定向，拼接连接的时候，因为会有测试环境和预发布环境以及线上环境的差异的存在
，所以免不了要进行环境的判断，但是如果每一个路由调用的时候都要写一个判断的逻辑的话，这样就太冗余了。

所以讲我们需要用到的链接全部放在 config.json 文件中，node 层使用 UA.config.targetParams 来进行相应的调用。

在页面中 直接通过 this.$store.state.targetParams 来进行调用，方便快捷

这样就比较简洁

> node层的 route > order.js
```js
if(shouldRedirect){ //请求来自集团并且带有pst参数则重定向
  return res.redirect(`${UA.config.zbjUserProjectUrl}/frame/common?path=${UA.config.projectUrl}/user/order/${orderId || ''}`)
}
```

> store 中将我们的配置文件挂载到全局的 state 中

```js
export const actions = {
  nuxtServerInit({ commit }, { req, res }) {
    const {
      pcProjectUrl, projectUrl,zbjUserProjectUrl
    } = req.app.locals.config;
     
    commit('init', {
      pcProjectUrl,
      projectUrl,
      zbjUserProjectUrl,
    });
  },
};

export const mutations = {
    init(state, initData) {
        Object.assign(state, initData);
    },
};
```

## 1.2 配置中心环境的配置

配置中心地址 [https://devops.zbj.com/#/project](https://devops.zbj.com/#/project)

配置中心分为不同的环境，在不同的环境，配置中心中配置 `config.json` 中定义好的公共的参数，这里如果配置中心有配置的话，首先参考配置中心的配置，而不读取本地` config.json `文件中的配置，如果想要一直使用` config.json `中的配置的话，那就在对应环境的配置文件中不进行配置即可。



# 2. 重定向中参数的读取以及重定向逻辑的判断

## 2.1 node层重定向

```js
UA.onGet('/user/order/:id', (req, res, next) => {
    res.redirect('redirectUrl')
}
```

## 2.2 参数读取

* `req.params` 读取的是连接 `order/123` 后的参数 `123`

* `req.query` 读取的是链接 `?params1=123&params2=456` 的 `{ params1:123,params2:456}`

## 2.3 逻辑判断具体参考业务逻辑 
这里的 `pst` 是一个重要的参数，是用来判断链接到底来自哪里（集团or财税）
```js
UA.onGet('/user/order/:id', (req, res, next) => {
  const orderId = req.params.id || '';
  const pst = req.query.pst || ''; //获取集团的pst参数，没有返回空
  
  const referer = req.headers.referer || ''; //获取网络请求的源
  let reg = new RegExp(`i.${UA.config.baseURI}`) // 检测链接来源的正则

  const shouldRedirect = (reg.test(referer) && pst) || (!reg.test(referer) && pst ) || (!reg.test(referer) && !pst ) //判断需要重定向的情况

  if(shouldRedirect){ //请求来自集团并且带有pst参数则重定向
    return res.redirect(`${UA.config.zbjUserProjectUrl}/frame/common?path=${UA.config.projectUrl}/user/order/${orderId || ''}`)
  }

  const ip = getIp(req);
  const userInfo = req.userInfo || {};
  const userId = userInfo.userId || '';
  
  const dataObj = {
    userId,
    orderId,
  };
  let descreaseListData = {
    userId,
    status: 1,
    ticketType: 2, // 类型 1:现金券、折扣券（默认） 2:减免券
    currentPage: 1,
    orderId,
  };
  const codeList = [
    {
      code: "pcxzz--ddxqycgg",//测试用例
      pageSize: 2,
      currentPage: 1,
    },
  ];
  const arr = [
    cityService.queryCityListByIp(ip),
    orderService.queryOrderDetail(dataObj),
    couponService.queryUserTicketList(descreaseListData),
    cmsService.batchGetContentFrontListPageByArea({
      codeList,
    }),
  ];
  return Promise.all(arr).then( async (data) => {
    let advertisementData = data[3] ? data[3] :[];
    Object.assign(res.locals, {
      cities: data[0],
      data: Object.assign(data[1] || {}, { countDown: '' }),
      descreaseList: data[2] ? data[2].data : [],
      advertisementData: advertisementData.length>0?advertisementData[0].contents:[],
    });
    next();
  });
});
```
# 3. 自定义404页面重定向操作

nuxtjs 只支持一个自定义的 errorPage, 当我们需要不同 errorPage 的时候就凸显了这个设计的局限性了

这里的处理方法是

* 在 common 中新建一个组件；

* 在 page 对应的 index.vue 文件中，引用该组件并且在 asyncData 的方法中进行错误页面的重定向

```js
export default {
    async asyncData({ res, error, redirect }) {
        const {
            data,
        } = res.locals;

        if (!data || !data.id) {
            // return error({ statusCode: 404, message: '订单查询失败' });
            redirect('/user/error') //不使用财税通用的错误页面，使用兼容集团的兼容页面
        }
 
        return {
            info: data,
        };
    },
}
```

