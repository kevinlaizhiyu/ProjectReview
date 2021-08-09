# 安装

## 兼容性 

Vue 不支持 IE8 及以下版本，因为 Vue 使用了 IE8 无法模拟的 ECMAScript 5 特性。但它支持所有兼容 ECMAScript 5 的浏览器。

## 语义化版本控制

Vue 在其所有项目中公布的功能和行为都遵循语义化版本控制。对于未公布的或内部暴露的行为，其变更会描述在发布说明中。

## 更新日志

最新稳定版本：2.6.12

每个版本的更新日志见 [GitHub](https://github.com/vuejs/vue/releases)。

## Vue Devtools

在使用 Vue 时，我们推荐在你的浏览器上安装 Vue Devtools。它允许你在一个更友好的界面中审查和调试 Vue 应用。

##  直接用 <code> script </code> 引入

直接下载并用 <code> script </code> 标签引入，<code> Vue </code> 会被注册为一个全局变量。

!> 在开发环境下不要使用压缩版本，不然你就失去了所有常见错误相关的警告!

- [开发版本 - 点击下载](https://cn.vuejs.org/js/vue.js)
- [生产版本 - 点击下载](https://cn.vuejs.org/js/vue.min.js)

## CDN

对于制作原型或学习，你可以这样使用最新版本：

```html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

对于生产环境，我们推荐链接到一个明确的版本号和构建文件，以避免新版本造成的不可预期的破坏：

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.12"></script>
```

## 如果你使用原生 ES Modules，这里也有一个兼容 ES Module 的构建文件：

```html
<script type="module">
  import Vue from 'https://cdn.jsdelivr.net/npm/vue@2.6.12/dist/vue.esm.browser.js'
</script>
```

你可以在 cdn.jsdelivr.net/npm/vue 浏览 NPM 包的源代码。

Vue 也可以在 unpkg 和 cdnjs 上获取 (cdnjs 的版本更新可能略滞后)。

请确认了解不同构建版本并在你发布的站点中使用生产环境版本，把 vue.js 换成 vue.min.js。这是一个更小的构建，可以带来比开发环境下更快的速度体验。

## NPM

在用 Vue 构建大型应用时推荐使用 NPM 安装[1]。NPM 能很好地和诸如 webpack 或 Browserify 模块打包器配合使用。同时 Vue 也提供配套工具来开发单文件组件。

```shell
# 最新稳定版
$ npm install vue
```

## 命令行工具 (CLI)

Vue 提供了一个官方的 CLI，为单页面应用 (SPA) 快速搭建繁杂的脚手架。它为现代前端工作流提供了 batteries-included 的构建设置。只需要几分钟的时间就可以运行起来并带有热重载、保存时 lint 校验，以及生产环境可用的构建版本。更多详情可查阅 [Vue CLI](https://cli.vuejs.org/)的文档。

!> CLI 工具假定用户对 Node.js 和相关构建工具有一定程度的了解。如果你是新手，我们强烈建议先在不用构建工具的情况下通读指南，在熟悉 Vue 本身之后再使用 CLI。

## 对不同构建版本的解释

在 NPM 包的 <code>dist/</code> 目录你将会找到很多不同的 Vue.js 构建版本。这里列出了它们之间的差别：


      | UMD	    |   CommonJS	|  ES Module (基于构建工具使用)	 | ES Module (直接用于浏览器)
完整版	|vue.js	  |  vue.common.js	| vue.esm.js	             |vue.esm.browser.js
只包含运行时版|	vue.runtime.js	|vue.runtime.common.js	| vue.runtime.esm.js  |	-
完整版 (生产环境)|	vue.min.js|	-|	-	| vue.esm.browser.min.js
只包含运行时版 (生产环境)|	vue.runtime.min.js|	-	|-|	-