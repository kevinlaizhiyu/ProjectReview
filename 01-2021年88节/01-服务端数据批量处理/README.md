# 
# 1. 数据为空的处理
【应用场景】
cms 后台数据请求到之后，要去判断某一项的数据是否为空的情况，由于存在大量重读的操作，所以将其封装成一个函数进行调用。
```js
//声明函数
const handle = (_index) =>
    _index < cmsInfo.length && cmsInfo[_index].contents ? cmsInfo[_index].contents : [];
//调用函数并返回
return {
    zbjInfo,
    ruleContentData: ruleContentData.content ? decode(ruleContentData.content) : "",
    topbarData : handle(21),
    navListTitle : handle(0),
    joinInfo: handle(1),
    goodsService: handle(2),
    // ...etc...
};
```

# 2. 巧妙利用解构赋值批量处理数据
【应用场景】
服务端请求到数据并处理之后，会产生许多的字段，并携带相应的数据，一个一个传递到前端比较麻烦，因此这里可以考虑将这些所有的字段全部打包进一个对象 `cmsInfo` 之后，在前端 `asyncData` 方法中再利用 `...cmsInfo` 结构这个对象,拿出所需的数据即可

> 这路由请求中，去掉繁琐的数据请求和处理的逻辑，将这些逻辑全部封装成方法移到外部，并在本逻辑中添加 `async/await` 处理异步问题
```js
// app>routes>88jie>2021.js
UA.onGet("/88jie/2021", async (req, res, next) => {
  const cmsInfo = await getCmsFloorData(needCmsCache);
  Object.assign(res.locals, {
    cmsInfo,
  });
  next();
});
```
> 在 `asyncData` 中直接结构出 `cmsInfo` 然后，再在 `return` 中直接 `...` 解构，这样做的好处就是，`只需要在将数据在node` 层塞到 `cmsInfo` 中即可，不需要多次重读解构
```vue
// app>nuxt>pages>88jie>2021>index.vue
<script>
export default {
  async asyncData({ res, store }) {
    const {
      cmsInfo = {},
    } = res.locals

    return {
      ...cmsInfo
    }
  },
}
</script>
```

