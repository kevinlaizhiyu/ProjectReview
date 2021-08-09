# 
# 概要
【应用场景】
用户登录状态之下，点击领券按钮，批量领券到用户的个人中心

【技术难点】
1. PC接口`get/post`传参区别
2. PC前后端联调的环境配置之 `t1`
3. `Mobile`请求PC端接口注意事项

# 1. PC接口`get/post`传参区别
## 1. schema 中的区别
有区别

```js
// app>schema>java-caishui-trade-api>TradeActivityTicketCodeOperateService.js
module.exports = {
  task: [
    {
      packageName: 'java-caishui-trade-api',
      service: 'TradeActivityTicketCodeOperateService',
      apiName: 'batchReceiveActivityTicket',
      method: 'post', // 或者'get'
      dubboMethodName: 'batchReceiveActivityTicket',
      action: 'java-caishui-trade-api/TradeActivityTicketCodeOperateService/batchReceiveActivityTicket',
    },
  ],
};
```
## 2. service 中的区别
没区别

> 接口方法中`Request<ActivityTicketReceiveRequestBatchDTO> request`
> - `Request`代表公共参数  -- 通常是 `data `
> - `ActivityTicketReceiveRequestBatchDTO` 代表业务参数 -- 视情况而定

> 所以我们一般拿到的参数是 `data`, 但是传递给服务的参数是 `{data}` , 即 `{data:{...数据data...}}`

```js
  // app>service>coupon.js
  batchReceiveActivityTicket(data){
    return api.tradeActivityTicketCodeOperateService.batchReceiveActivityTicket.post({ data });
  }
```

## 3. route 中的区别
有区别

- 获取参数有区别 \
  `get方法>---> req.query.params`、`post方法>---> req.body.params`。
  

- 请求服务传递参数没区别。 \
  从 `req` 中拿到相应的参数之后，全部塞进 `data` 这个对象中，在将 `data` 作为参数，放到请求的服务方法中
  
```js
// app>route>api>coupon.js
UA.onPost('/api/user/batchReceiveActivityTicket', cors({
  origin: [/\.zbj\.com$/, /\.zbjdev\.com$/, /\.zhubajie\.la$/],
  credentials: true,
}), (req, res) => {
  const data = {
    zbjId: parseInt(req.userInfo.userId) || '',
    zbjPhone:req.userInfo.mobile||'',
    activityTicketCodeRuleIds: req.body.activityTicketCodeRuleIds || [],
    additionBizType: req.body.additionBizType || [1],
    opportunitiesDTO: req.body.opportunitiesDTO || {},
  };
  return couponService.batchReceiveActivityTicket(data).then((resData) => {
    res.json(resData);
  })
    .catch((err)=>{
      console.log(err)
    });
});
```
## 4.页面调用 axios 的区别
有区别

- `get` 方式的 `axios` 请求需要将对象再包一层，并赋值给一个参数 `params` 。
-  `post` 方式的 `axios` 请求直接传递 `data` 对象即可。

```js
// get 请求传参
let data = {
  posterUrl: encodeURIComponent(urlLink)
}
this.$axios.get('/annual-report/queryRepPoster',{params:data})
```

```js
// post 请求传参
const data = {
  activityTicketCodeRuleIds:this.handleCouponsId(),
  additionBizType:[1],
  opportunitiesDTO:{
    chanceTypeId:132,
    requestType:100001,
    remark:"PC-2021年88节干货礼券包"
  }
}
vm.$axios.post("/user/batchReceiveActivityTicket", data )
```

# 2. PC 前后段联调的t1环境配置 

```json
// 根目录下的 config.json 文件 
"dubboVersionInfo": "java-caishui-trade-api=trade-t1",
```

# 3. Mobile请求PC端接口的注意事项

## 1. get请求
- 开发过程中，请先启动PC的项目
- PC端对应的 route 接口做跨域兼容处理
```js
//使用 cors 中间件
  cors({
    origin: [/\.zbj\.com$/, /\.zbjdev\.com$/, /\.zhubajie\.la$/],
    credentials: true,
  })
```
- `get` 请求不能够传递对象参数（譬如数组），只能将一般的键值对象，编排成 `query` 字符串放在地址栏请求
- 如果使用 `get` 请求传递数组对象参数，项目中会进行安全报警 [Error: invalid csrf token 跨站请求伪造](https://yijiebuyi.com/blog/74528e6546834ebfec47daf4e222d621.html)  
 
## 2. post请求

- 直接在 `mobile` 项目中和PC一样配置 `schema` `service` `route` 
- 然后调用即可
