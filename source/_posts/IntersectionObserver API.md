---
title: IntersectionObserver API
tags:
  - IntersectionObserver
  - web APIs
url: 217.html
id: 217
categories:
  - technology
date: 2017-08-21 09:35:54
---

previously 还在用监听scroll事件+getBoundingClientRect()+节流函数实现lazyload么，你已经out了 what? 是浏览器原生的构造函数，该API可以自动"观察"元素是否可见，Chrome 51+ 已经支持。由于可见（visible）的本质是，目标元素与视口产生一个交叉区，所以这个 API 叫做"交叉观察器"。 how?

    var io = new IntersectionObserver(callback, option)
    
    // 开始观察
    io.observer(document.getElementById('#example'))
    
    // 停止观察
    io.unobserver(element)
    
    // 关闭观察器
    io.disconnect()
    

callback一般会触发两次，进入/离开视口。另外回调函数以IntersectionObserverEntry对象作为参数。

    {
      time: 3893.92,
      rootBounds: ClientRect {
        bottom: 920,
        height: 1024,
        left: 0,
        right: 1024,
        top: 0,
        width: 920
      },
      boundingClientRect: ClientRect {
         // ...
      },
      intersectionRect: ClientRect {
        // ...
      },
      intersectionRatio: 0.54,
      target: element
    }
    

*   time：可见性发生变化的时间，是一个高精度时间戳，单位为毫秒
*   target：被观察的目标元素，是一个 DOM 节点对象
*   rootBounds：根元素的矩形区域的信息，getBoundingClientRect()方法的返回值，如果没有根元素（即直接相对于视口滚动），则返回null
*   boundingClientRect：目标元素的矩形区域的信息
*   intersectionRect：目标元素与视口（或根元素）的交叉区域的信息
*   intersectionRatio：目标元素的可见比例，即intersectionRect占boundingClientRect的比例，完全可见时为1，完全不可见时小于等于0

Option对象 threshold属性决定了什么时候触发回调函数。它是一个数组，每个成员都是一个门槛值，默认为\[0\]，即交叉比例（intersectionRatio）达到0时触发回调函数。 另外在容器内滚动也会影响目标元素的可见性。root属性决定目标容器所在的容器节点。容器元素必须为目标元素的容器节点。 （不清楚此属性的用法）rootMargin用来定义根元素的margin，用来扩展或缩小rootBounds这个矩形的大小，从而影响intersectionRect交叉区域的大小 notice 规格写明，IntersectionObserver的实现，应该采用requestIdleCallback()，即只有线程空闲下来，才会执行观察器。这意味着，这个观察器的优先级非常低，只在其他任务执行完，浏览器有了空闲才会执行。