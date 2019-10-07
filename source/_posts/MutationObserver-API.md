---
title: MutationObserver API
tags:
  - MutationObserver
  - web APIs
url: 236.html
id: 236
categories:
  - note
  - technology
date: 2017-08-23 09:15:27
---

previously 是否想过未来HTML5新特性可能取代目前火爆的MV*框架 what 监控DOM节点的属性、子抽象节点 how

    // Firefox和Chrome早期版本中带有前缀
    var MutationObserver = window.MutationObserver || window.WebKitMutationObserver || window.MozMutationObserver
    
    // 选择目标节点
    var target = document.querySelector('#some-id');
    
    // 创建观察者对象
    var observer = new MutationObserver(function(mutations) {
      mutations.forEach(function(mutation) {
        console.log(mutation.type);
      });
    });
    
    // 配置观察选项:
    var config = { attributes: true, childList: true, characterData: true }
    
    // 传入目标节点和观察选项
    observer.observe(target, config);
    
    // 随后,你还可以停止观察
    observer.disconnect();
    

属性

描述

childList

如果需要观察目标节点的子节点(新增了某个子节点,或者移除了某个子节点),则设置为true.

attributes

如果需要观察目标节点的属性节点(新增或删除了某个属性,以及某个属性的属性值发生了变化),则设置为true.

characterData

如果目标节点为characterData节点(一种抽象接口,具体可以为文本节点,注释节点,以及处理指令节点)时,也要观察该节点的文本内容是否发生变化,则设置为true.

subtree

除了目标节点,如果还需要观察目标节点的所有后代节点(观察目标节点所包含的整棵DOM树上的上述三种节点变化),则设置为true.

attributeOldValue

在attributes属性已经设为true的前提下,如果需要将发生变化的属性节点之前的属性值记录下来(记录到下面MutationRecord对象的oldValue属性中),则设置为true.

characterDataOldValue

在characterData属性已经设为true的前提下,如果需要将发生变化的characterData节点之前的文本内容记录下来(记录到下面MutationRecord对象的oldValue属性中),则设置为true.

attributeFilter

一个属性名数组(不需要指定命名空间),只有该数组中包含的属性名发生变化时才会被观察到,其他名称的属性发生变化后会被忽略.