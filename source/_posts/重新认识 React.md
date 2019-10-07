---
title: 谈谈对 React 的理解
tags:
  - React
categories:
  - technology
date: 2020-04-05 18:35:00
---

## 是什么

- 犹如一把利器，帮助前端攻城狮披荆斩棘拿下一个个需求。
- 犹如一部电影，总是能带给前端研发惊喜提升工作幸福感。
- 犹如一盏明灯，在繁杂的前端生态中指引方向。
- ...
- 我理解它的本质是一种编程思维

## 编程思维

React 使用基于组件的声明式编程思维。

组件是抽象的概念，它可以表示视图、样式甚至是逻辑，网页通常由许多视图构建而成，任意网页均能分解成多个组件，组件可以分解成子组件。使用 React 简洁清晰得可预测得自顶而下得声明组件的关系、状态、行为逻辑，从而构建出用户界面。

## 做了什么

### 核心之一 ReactComponent & ReactElement & instances

React.createElement 高度类似于 document.createElement，输入类型（字符串或ReactComponent）、props、子 Element(s)，输出 ReactElement 作为原子单元构成 Virtual DOM。

ReactElement 本质上是 Javascript Object, 其实与 HTMLElement 大同小异，它向开发者描述一个 Element 或 Element tree 的基本“面貌”。换句话说 ReactElement 等同于 light HTMLElement。

React 通过 ReactElement 的描述生成 instances 渲染至屏幕

### 核心之二 Virtual DOM & Reconciliation & Update

Virtual DOM 高度类似于 DOM，是一颗有序分层结构的树，描述多个 Elements 的联系，或相邻或嵌套或其他，若把联系当作线， Element 当作点，点和线构成“骨架”，点线越多构成的“骨架”越完整。

React 应用总是从任一特定状态过渡至另一个，并同时生成一颗新树。为降低开销提高应用性能，React 使用 Reconciliation 对比新/旧树差异从而决定哪些部分需要改变，在浏览器应用中，这些改变被翻译为 DOM 操作的集合，最后有序执行，完成一次 Update。

### 核心之三 React Fiber

React 团队花两年时间重新实现核心算法，该实现晦涩难懂。据核心研发人员介绍，React Fiber 的设计目标是增强对动画、布局、手势的适用性，其主要功能是增量渲染：能够将渲染工作分解成多块并分散到多个帧中。

值得一提的是 Fiber 架构重写了 reconciler，主要差异在于使用 keys 提升列表的渲染性能。

### 其他

React 抹平浏览器部分 Web APIs 的差异性并做了一些友好的封装比如监听器的语法，相比 DOM0 级事件 key 从小写改成驼峰，value 从传递字符串变成传递方法，封装禁止默认行为和阻止事件冒泡等等。

## 使用 React 的优势

> 终极目标是提升生产力

- 可复用的组件，减少冗余代码块提高可维护性
- 视图、样式、逻辑、状态都可抽象成组件，形成统一的编程思维，降低理解成本提高协作效率
- 跨端能力
- 实现 Virtual DOM，减少 DOM 操作的开销从而提高 Web 应用的性能
- 相比 html + css + js，纯粹的 js 使开发者能够无所拘束的组织代码，充满想象空间

## 小结

React 构建用户界面，它不是“万金油”，它不处理 router、canvas、audio/video、Web RTC、fetch 等等 Web APIs，对于构建富交互 SPA/MPA 而言是不够的，我理解这是促使 React 社区蓬勃发展的原因之一。React 真香。
