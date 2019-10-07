---
title: AMP与PWA
url: 762.html
id: 762
categories:
  - technology
date: 2018-05-17 23:45:19
tags:
---

PWA
===

what?

*   Progressive Web App
*   匹敌Native APP的沉浸式全屏web应用，支持offline、push、硬件感知等功能
*   Chrome团队希望用一个新的buzzword（流行词）来影响、改变用户的期待

why?

*   更方便的安装方式
*   更小的应用体积
*   更快的开启速度

problem?

*   国内iphone使用者多，不支持PWA
*   国内Android被各大手机厂商定制化，支持PWA对收益关系不大
*   移动端Chrome使用人数较少
*   依赖GCM的通知推送
*   须各大移动端浏览器支持

how?

*   Service Workers 配合 Cache API实现离线应用
*   PUSH API实现推送通知
*   Add to HomeScreen
*   Hardware API实现硬件感知
*   Web Manifest辅助实现离线应用
*   HTTPS是数据安全保证

不废话了！！来[干货](https://github.com/SangKa/PWA-Book-CN/blob/master/README.md)

AMP
===