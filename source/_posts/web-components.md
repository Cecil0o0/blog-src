---
title: Web Components
tags:
  - Polymer
  - Web Components
  - webcomponentsjs
url: 980.html
id: 980
categories:
  - technology
date: 2018-10-24 13:38:21
---

Compatibility
=============

Web Components are now implemented natively on Safari and Chrome (~70% of installed browsers), and run well on Firefox, Edge, and IE11 using [polyfills](https://github.com/webcomponents/webcomponentsjs)

Best Practices
==============

[Google Polymer](https://github.com/Polymer/polymer)

Concept
=======

原生级web组件，是一种现代化web开发模式

Features（vs React、Vue）
======================

1.  提供原生级的作用域隔离（包括js与css），实现真正意义上的组件化

Short Boards
============

1.  社区暂不活跃，导致优质组件不多等等
2.  最低仅支持IE11，而且需要polyfills，兼容差一些
3.  是否支持reactive components
4.  API不稳定

How
===

Web Components特性主要由四项技术构成Custom Elements、Shadow DOM、HTML Templates、HTML Imports

> Custom Elements V0 API、Shadow DOM V0 API、HTML Imports将在2019年4月于M73被移除，并配套迁移至Custom Elements V1、Shadow DOM V1 API、ES Module API

Timer Demo

    window.customElements.define('c-timer', class extends HTMLElement {
      constructor() {
        super()
        const shadowRoot = this.attachShadow({
          mode: 'open'
        })
    
        console.log(shadowRoot)
    
        const text = document.createElement('span')
        text.classList.add('testCls')
        text.textContent = this.getAttribute('start')
    
        shadowRoot.appendChild(text)
    
        const style = document.createElement('style')
        style.textContent = `
          .test {
            font-size: 3vw;
          }
        `
        shadowRoot.appendChild(style)
    
        setInterval(() => {
          text.textContent = Number(text.textContent) + 1
        }, 999)
      }
    })
    

使用

    <c-input start="111" />