---
title: CSP
tags:
  - CSP
  - upgrade-insecure-requests
url: 610.html
id: 610
categories:
  - technology
date: 2018-04-13 09:58:57
---

what
====

什么是CSP。CSP（Content-Security-Policy）是一个附加的安全层，用于避免和缓解某些类型的攻击，包括XSS和注入等。CSP向后兼容，浏览器不支持会ignore，默认对网页执行标准的同源策略。可在网站中增加CSP头部，此外根据CSP的标准特点，可通过标签来配置。

includes
========

*   源列表（定义源的有效性）

可通过配置以空格为分隔符的有效的目标主机地址字符串 如 `http://*.qingf.me blog.qingf.me https://blog.qingf.me`

*   关键字 （定义资源的可访问性）

`'none'` 代表空集：即不匹配任何URL `'self'` 代表文档同源，包括相同的URL协议和端口号。 `'unsafe-inline'` 允许使用内联资源，如内联的`<script>`、`javascript:URL`、内联事件处理函数和内联`<style>` `'unsafe-eval'` 允许使用eval()等通过字符串创建代码的方法

    Content-Security-Policy: default-src 'self';img-src 'self' data:;
    

chrome-only
===========

`upgrade-insecure-requests` 强制网页内http以https发送请求 最低版本为chrome43

    <meta name="Content-Security-Policy" content="upgrade-insecure-requests"/>