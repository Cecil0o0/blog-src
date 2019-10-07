---
title: React事件系统
tags:
  - event system
  - react
url: 942.html
id: 942
categories:
  - technology
date: 2018-07-25 00:09:18
---

*   基本用法
*   基本原理
    *   概述
    *   绑定与注册
    *   触发与dispatch
*   合成事件（SyntheticEvent）
    *   概念
    *   优势
*   事件池概念

基本用法
====

    class DemoCom extends React.Component {
        handleClick(e) {
            alert('click')
        }
        render() {
            return <div onClick={this.handleClick}></div>
        }
    }
    

注意点

*   驼峰格式属性名
*   值为事件处理器而非字符串
*   v14以后版本必须使用e.preventDefault()显示阻止浏览器默认事件而非return false

基本原理
====

### 概述

通过`事件委托`与`合成事件`配合`事件池`与`批处理队列`机制实现Event System

### 绑定与注册

所谓绑定，即建立事件处理器与实际Dom的关联。 所谓注册，即push事件处理器到关联Event Type的执行（callback）队列中等待派发。 而在React中，绑定与注册过程需要经过如下步骤，分别为：

1.  React Component在mountComponent以及updateComponent阶段都需要执行`_updateDOMProperties`处理JSX属性，然后遍历其属性并识别出事件属性
2.  （由于`所有事件绑定在document`上）通过ReactEventListener类的enqueuePutListener原型方法将事件进行注册，由Event Type与React对象进行了`二维划分`
3.  通过EventPluginHub类的putListener原型方法`储存事件`等待派发

### 触发与dispatch

所谓dispatch，即依次执行对应Event Type相关的回调方法 而React自身实现一套捕获/冒泡机制并使用批处理的方式进行事件dispatch，关键步骤如下：

1.  用户触发绑定在document上的TopLevel Event，执行ReactEventListener类dispatchEvent原型方法，将“bookKeeping”（可以理解为申请执处理）方法push到React内置的批处理队列中等待执行
2.  当批处理tick到达，执行ReactBrowserEventEmitter.handleTopLevelImpl原型方法，该方法获取Dom与React对象，并且找到该React对象的祖先对象生成数组，从当前React对象向祖先遍历从而实现`冒泡`，并执行相同事件类型callback（也就是事件处理器）
3.  执行ReactBrowserEventEmitter.handleTopLevel，通过合成事件构造器构造或者被析构后的事件对象重用两种方式生成`合成事件`（不同事件类型合成事件不一样），然后进入批处理队列等待执行
4.  当批处理tick到达，遍历events，顺序执行event，然后遍历event相关的listeners，for循环依次执行，如果遇到stopPropagation则break，否则`执行事件处理函数`

SyntheticEvent
==============

### 概念

对浏览器原生事件作一层封装的增强型事件对象，称为合成事件。 注意

*   合成事件被`Proxy`封装
*   原则上`不建议`和原生事件混用

优势
--

*   兼容性。抹除IE与W3C标准差异，并提供与原生相同的API
*   便捷性。提供onCopy等组合后事件，另外提供onClickCapture在捕获阶段执行事件处理器
*   效率性。事件载体复用。

其他
--

Event System通过事件池与批处理机制对事件执行进行了性能优化，但并不见得比原生Event System性能好

事件池
===

概念
--

用于存放被`析构`的event栈。

如何形成
----

每次event对象使用完毕后（未使用event.persist()情况下）在release阶段生成并push至event栈。

如何利用
----

React内部在处理事件返回值时，分两种情况。

1.  使用event.persist()，强制使用nativeEvent，此时不会使用事件池来优化。
2.  不调用时，React返回一个合成事件。先判断事件池中是否存在可用的event实例对象，如果有则重用该实例并通过EventConstructor构造出新的合成事件后返回给事件处理器，反之则使用new EventConstructor()构造全新合成事件，并在使用完毕即进入release阶段，该阶段React将析构该合成事件后把event壳的引用push到事件池中等待复用。