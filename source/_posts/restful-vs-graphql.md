---
title: RESTful vs GraphQL
tags:
  - GraphQL
  - RESTful
url: 469.html
id: 469
categories:
  - technology
date: 2017-10-08 15:41:37
---

## what

> 接口协议标准或称查询语言

## why is GraphQL

> RESTful API利用HTTP原生支持的四种状态(PUT DELETE GET PATCH)配合endpoint即可实现数据增删查改，但是其主要痛点在于数据集合分散，客户端不易查询复杂关系的数据结构，因为RESTful API注重的数据集合的存取，而忽略数据间的网状关系。 客户端大量并发请求和对补充数据的二次请求可能会造成很大的麻烦，尤其是响应时间。一个解决方案是设计足够多的资源来满足单次的请求。但是，这造成服务端扩展及维护困难。 GraphQL在这样的背景下应运而生，由Facebook公司于2012年推出。 GraphQL相较于RESTful最大的特性和优势就在于其精确定义数据的能力，它使你能够以单次调用代替多次RESTful请求拿到你所具体指定的数据。 

## how

query

```graphql
    query {
      client(id: 1) {
        id
        name
      }
    }
```

response

```graphql
    {
      "data": {
        "client": {
          "id": "1",
          "name": "Uncle Charlie"
        }
      }
    }
```

## Refs

- [RESTful API 设计最佳实践](http://blog.jobbole.com/41233/)
- [The Github GraphQL API](https://githubengineering.com/the-github-graphql-api/)
