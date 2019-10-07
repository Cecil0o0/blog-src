---
title: 概述javascript部分核心机制
tags:
  - event loop
  - excution context
  - scope chain
  - this
url: 835.html
id: 835
categories:
  - technology
date: 2018-06-09 00:37:34
---

javascript - scope chain - context（this 指向） - excution context

# scope chain

即执行环境（execution environment） 即一条`执行时`才会建立的`定义时`已经决定了（liao）的链。 ![](https://blog.qingf.me/wp-content/uploads/2018/06/scopechain.png)

# context（this 指向）

this === context

    var name = '亚索'
    // 1-------------------------------------------------------
    var obj = {
        name: '锐雯',
        readName: function() {
            console.log(this.name)
        }
    }
    obj.readName()
    // bind用法
    obj.readName.bind(this)  //invalid
    var otherFn = obj.readName
    otherFn.bind(this)
    otherFn()
    // call用法
    obj.readName.call(this)
    // apply用法
    obj.readName.apply(this)
    // 2--------------------------------------------------------
    function readName() {
        (function ad() {
            console.log(this.name)
        })()
    }
    readName()
    // 3-------------------------------------------------------
    function Person(name) {
        this.name = name
    }
    console.log(new Person(name).name)
    // 4-------------------------------------------------------
    var obj2 = {
        readName: () => {
            console.log(this.name)
        }
    }
    obj2.readName()
    obj2.readName.call(this)

结果请复制到控制台执行后查看结果

> 注：箭头函数其实没有自己的 this 指针，它只是一个语法糖

    箭头函数
    var bbb = {
        name: '锐萌萌',
        normalFn: function b () {
            return () => {
                console.log(this.name)
            }
        },
        arrowFn: function b () {
            let _this = this
            return function () {
                console.log(_this.name)
            }
        }
    }

# excution context

我们理解 JS 引擎时所了解的栈即为执行环境栈。比如我们用语言模拟一段脚本执行到 function 后发生的一系列动作。 `创建阶段（creation phase）`

1.  创建一个变量对象（也称活动对象）常常由定义在当前执行环境中的内部变量、内部 function 以及参数组成，且仅能被解析器访问
2.  初始化作用域链
3.  this 指针被最终确定
4.  推入堆栈并获取控制权

`执行阶段（execution phase）`

1.  代码在当前执行环境中执行，执行完成后 pop 出当前执行环境，并将控制权移交给上一级执行环境
