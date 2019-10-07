---
title: OAUTH2.0
url: 328.html
id: 328
categories:
  - technology
date: 2017-09-16 00:18:57
tags:
---

what? OAUTH协议为用户资源的授权提供了一个安全的、开放而又简易的标准。 原理就是USER为TPA授权，有授权之后，TPA获取令牌，进而凭令牌获取部分被保护资源 [阮一峰日志](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html) why? 减少用户登录成本，提升UX how?

*   [授权码模式](http://image.qingf.me/tech/authorization_code_schema.png)
*   [简化模式](http://image.qingf.me/tech/OAUTH2.0_simple_schema.png)
*   密码模式
*   客户端模式

* * *

### 微信开放平台以授权码模式实现

*   共经过三次关键交互
    1.  微信用户请求登录TPA，TPA向WeChat Open请求OAUTH2.0登录，WeChat Open返回授权临时票据(code)
    2.  TPA根据appid、appsecret和code返回access_token
    3.  TPA凭借接口调用凭证access_token获取用户基础信息
*   网站应用接入形式
    1.  外链式
    2.  内嵌式

[微信开放平台传送门](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419316505&token=bc40d55ba368469759d019068666293effcbb057&lang=zh_CN)