---
title: 概述Vue SSR工作原理以及工程实践
tags:
  - Isomorphic
  - seo
  - ssr
  - vue
  - 可扩展性
  - 工程化
url: 900.html
id: 900
categories:
  - technology
date: 2018-07-01 14:08:47
---

- 工作原理
  - SSR 优劣势
  - 详细构建过程
  - 运行流程概述
- 实践
  - 工程目录结构
  - 同构中的差异
  - 踩坑

# 工作原理

## SSR 优劣势

- 优势
  - 首屏渲染加快（相当于省去 client 端 fetch + load bundle 并且初始化 vue 的时间）
  - 支持 SEO

## 构建过程

- 入口文件二：entry-server。需要依次作如下逻辑处理
  - 新建 vue、vue-router、vuex 实例（注意不能复用实例）
  - app 挂载 router 和 store 插件
  - router 根据当前请求即 req.url，根据路由匹配到 Components，如果存在 asyncData 则`异步获取所有数据`后 resolve app 实例
  - registerModule 并存在 state 时，将 state 挂载到 context.state 下，并由 renderer 将其`序列化`
  - renderer 可以在 HTML 中收集并内联组件 css，并自动注入首屏关键 css（critical css）
  - renderer 通过 app 实例 render 为 HTML string，并返回给 client 端
- 入口文件一：entry-client。需要依次作如下逻辑处理
  - 同样的，初始化 app 实例并挂载 router,store 插件
  - 当路由 onReady 后，挂载`<div id="app" data-server-rendered="true">...</div>`真实 DOM，由于此时通过`data-server-rendered`已知晓由服务端渲染完毕，因此客户端走激活（hydrate）流程而不是重新渲染替换节点`#app`
  - 另外数据预取逻辑和服务端略有不同，我用的是`按需加载`策略也就是通过 mixin 混入 beforeMount 生命周期去预取数据。为了防止 double-fetch 数据，这里需要作出调整即在挂载 app 之后再混入。
- 然后 webpack 通过不同入口分别打包 client-manifest.json 以及 server-bundle.json，应用于 renderer
- 其中 client-manifest 主要用于标记程序依赖 chunk，server-bundle 中则存储并映射打包后所有的 bundle 用于渲染。renderer 具有程序的所有构建信息，因此它可以自动推断和注入资源 prefetch 与 preload 指令，以及 css、javascript 标签到 HTML 中。与此同时，为避免 client 瀑布式请求（waterfull request）以及达到最快的 TTI（Time-To Interactive），只有应用程序首屏依赖的 chunk 会以 preload 形式注入，暂未使用的异步 chunk 通过 prefetch 形式注入。

## 运行流程概述

- server 端负责渲染首屏的工作比如预取数据，全局 state 初始化（不包括 actions,mutations 以及 getters），直出首屏完整 DOM
- client 端负责 hydrate（激活）SPA，比如 store replaceState，注册 store 模块

# 实践

## 工程依赖的核心库版本概览

        "vue": "^2.5.16",
        "vue-router": "^3.0.1",
        "vue-server-renderer": "^2.5.16",
        "vuex": "^3.0.1"
        "vue-loader": "^15.2.4",
        "vue-style-loader": "^4.1.0",

## 工程目录结构

请移步[我的 github](https://github.com/Cecil0o0/ssr-demo)查看（欢迎 star）

## 可执行命令

- npm run dev
- npm run serve
- npm run serve:watch
- npm run build:client-standalone
- npm run build:client-analyze

## 同构中的差异

- server 端和 client 端渲染不同页面时在各自的生命周期中（server 是 beforeCreated and created，client 更关注 mounted and more life cycles）可能需要处理`相同`的事情，比如网页 title 和通用头部管理等

用全局 Mixin 或者 Class 继承来做。个人更倾向于`全局Mixin`，因为开发时`少写冗余代码`，但因此损失一点运行时性能比如内存和计算力

## 踩坑

- TypeError: Cannot read property 'render' of undefined

最终发现在 webpack.base.conf.js 中 output.filename 后面`带了hash`导致可能在生成 server-bundle.json 的时候匹配不到该 chunk 导致`json文件构建失败`，从而导致 vm 为 undefined，最终抛出以上错误信息。解决方案是把该配置项放在 webpack.client.conf.js 与 webpack.server.conf.js 中`分别`配置

- loader 执行顺序从后到前

也就是说不管是字符串`style-loader!css-loader!stylus-loader`还是数组形式`['style-loader', 'css-loader', 'stylus-loader']`，执行顺序都是从 stylus-loader -> css-loader -> style-loader

- style-loader 中依赖 window，导致 ssr 失败

用 vue-style-loader 代替

- 服务端渲染开发时做 hmr 有坑

- 当在 ssr 工程中引入 standalone 时需要做一些处理比如

如果是 standalone 模式则`挂载前`mixin 预取数据的生命周期钩子用于 client fetch asyncData，如果是 ssr-client 模式则采用`挂载后`再 mixin 此钩子的策略，为防止 client hydrating 出现 double-fetch 的情况
