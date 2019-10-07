---
title: 读Vue3.0 Function-Based API的RFC有感
categories:
  - technology
date: 2019-10-07 07:05:21
---

### 总结

> Class API 唯一的优势是熟悉，但使用 Function 可提供给开发者更多裨益。受益于 React Hooks 但从根本上不同的是 Function-Based API 扎根于 Vue 自身的响应式系统。另外这些 APIs 解决了许多 React Hooks 上的使用问题。

React Hooks 使用问题有：

- 在每一次 render 过程中，`setup()` function 只调用一次，而 React Hooks 会重复调用，前者减少 GC 压力

### 核心理念

- 更优雅的逻辑复用（使用`setup()` function）
  > 优雅之余还能解决问题。对于 2.x 逻辑复用通常使用 mixin、HOC、Renderless components(通过作用域插槽)，而使用上述存在三个问题分别是属性来源不清楚、命名空间冲突以及性能（HOC 和 Scoped slots 需要额外有状态的组件实例开销）。
- 更好的代码组织
  > 根据功能粒度来组织代码，相比于 options API，代码更内聚。
- 更好的类型推断
  > function 天生是类型友好的，因为方法的 prototype 已经实现了完整的类型支持。
- 缩小打包体积
  > 在标准的压缩规则下，函数与变量名可被缩短然而对象或类方法与属性不行。
