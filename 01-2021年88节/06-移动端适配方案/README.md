# 
# 概要
- `vm` 简单直接，但是计算繁琐
- 结合 `less` 之后的 `vm` 运用稍好，但是需要配置许多 `mixin` 函数，优化版本会好一些，缺点是浏览器调试样式变形
- `flexble.js` 可以参考，但是不建议使用，原因是视窗尺寸参考字体大小来适配，本身这个做法就有待商榷，表示 `rem` 时代已过时
- 推荐 `postcss` 用法，`nuxtjs` 框架自带，配置插件之后能够自行处理单位的转换
# 1. vw 
根据屏幕的宽度进行适配
```css
.container{
  width:15vw;
}
```
# 2. less + vw
根据屏幕的宽度进行相应的适配，但是简化了计算的过程
```less
// common_mixin.less
.width(@px){
  width:unit((@px/750)*100,vw)
}
.max-width(@px){
  max-width:unit((@px/750)*100,vw)
}
.height(@px){
  height: unit((@px/750)*100,vw);
}
.font-size(@px){
  font-size: unit((@px/750)*100,vw);
}
// 在目标页面引入
@import url('./common.less');
```

优化版本
```less
@basePageSize:750
.px2rem(@name, @px){
  @{name}: @px * 100 / @basePageSize * 1vw;
}
```

终极版本
```less
.px2vw(@name, @px){
  @{name}: @px / 7.5 * 1vw;
}
```


# 3. flexble.js

1. 内联引入 `flexble.js`
2. 修改 `meta` 标签
3. 设置 `less` `scss` 预处理语言 or 安装 `px2rem` 编辑器插件实现 `px` 顺利转换 `rem`

[参考文章1](http://caibaojian.com/mobile-responsive-example.html)

[flexble.js github地址](https://github.com/amfe/lib-flexible)

[flexble.js文档地址](https://github.com/amfe/article/issues/17)

[flexble.jsCDN地址](http://g.tbcdn.cn/mtb/lib-flexible/0.3.2/??flexible_css.js,flexible.js)

【注意】

# 4. postcss

- `nuxtjs` 自带 `postcss`

- `npm` 安装 `postcss-px-to-viewport --save`

- 在 `nuxt.config.js` 中进行插件的配置

```js
    build: {
      postcss: {
        plugins: {
          autoprefixer: {},
          "postcss-px-to-viewport": {
            unitToConvert: 'px', // 需要转换的单位，默认为"px"
            viewportWidth: 375, // 视窗的宽度，对应设计稿的宽度
            viewportUnit: 'vw', // 指定需要转换成的视窗单位，建议使用 rem
            fontViewportUnit: 'vw', // 字体使用的视口单位
            unitPrecision: 13, // 指定`px`转换为视窗单位值的小数后 x位数
            exclude: [
              /(\/|\\)node_modules(\/|\\)/,
              /\/app\/nuxt\/components\/finance\/finance-data/,
            ], // 排除的文件
          },
        },
      },
    },
```
【参考文章1】[nuxtjs 官网 postcss](https://zh.nuxtjs.org/docs/2.x/features/configuration#postcss-plugins)

【参考文章2】[移动端布局之postcss-px-to-viewport（兼容vant）](https://www.cnblogs.com/zhangnan35/p/12682925.html)

【参考文章3】[postcss-px-to-viewport npm的地址](https://www.npmjs.com/package/postcss-px-to-viewport)
