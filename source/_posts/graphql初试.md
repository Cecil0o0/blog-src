---
title: graphql初试
tags:
  - GraphQL
  - koa
  - vue-apollo
url: 876.html
id: 876
categories:
  - technology
date: 2018-06-16 19:09:19
---

*   基本用法
*   GraphQL概述
    *   GraphQL基本语法特性
    *   GraphQL类型系统
    *   GraphQL类型系统内置基础类型
    *   GraphQL类型系统内置修饰符
    *   GraphQL工作原理
    *   GraphQL执行过程
*   Vue工程接入GraphQL

基本用法（如何去用）
==========

package.json

    "dependencies": {
        "apollo-server-koa": "^1.3.6",
        "graphql": "^0.13.2",
        "graphql-import": "^0.6.0",
        "graphql-tools": "^3.0.2",
        "koa": "^2.5.1",
        "koa-bodyparser": "^4.2.1",
        "koa-router": "^7.4.0",
        "koa-websocket": "^5.0.1"
    },
    "devDependencies": {
        "babel-cli": "^6.26.0",
        "babel-preset-env": "^1.7.0"
    }
    

server.js

    import koa from 'koa'
    import koaRouter from 'koa-router'
    import koaBody from 'koa-bodyparser'
    import websocketify from 'koa-websocket'
    import { graphqlKoa, graphiqlKoa } from 'apollo-server-koa'
    import { makeExecutableSchema } from 'graphql-tools'
    
    const app = websocketify(new koa())
    const router = new koaRouter()
    const PORT = 3000
    
    // fake data
    const moments = [
      {
        user: {
          id: 1000,
          name: '锐雯',
          avatar: 'http://imgsrc.baidu.com/imgad/pic/item/42a98226cffc1e17d31927154090f603738de974.jpg'
        },
        main: {
          content: '这是一条朋友圈',
          pics: [
            'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1529219875063&di=bc0bcc78ae800c1c21c198f52697f515&imgtype=0&src=http%3A%2F%2Fimgsrc.baidu.com%2Fimgad%2Fpic%2Fitem%2F4a36acaf2edda3ccd53548ea0be93901203f9223.jpg',
            'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1529219893624&di=8d9e418df27e1fdb6afb1d993801a980&imgtype=0&src=http%3A%2F%2Fimgsrc.baidu.com%2Fimgad%2Fpic%2Fitem%2F3801213fb80e7beca9004ec5252eb9389b506b38.jpg'
          ]
        },
        comments: [
          {
            user: {
              id: 233122,
              name: '亚索'
            },
            reply: '面对疾风吧'
          }
        ]
      }
    ]
    
    const typeDefs = `
      type Query {
        moments: [Moment]
      }
      type Mutation {
        addComment(
          entity: Add_Comment
        ) : Comment
      }
      type Moment {
        user: User
        main: Main
        comments: [Comment]
      }
      type User {
        id: Int
        name: String
        avatar: String
      }
      type Comment {
        user: User
        reply: String
      }
      type Main {
        content: String
        pics: [String]
      }
      input Add_User {
        id: Int
        name: String
      }
      input Add_Comment {
        user: Add_User
        reply: String
      }
    
      # 定义graphqlf服务哪个是RootQuery以及RootMutation
      schema {
        query: Query
        mutation: Mutation
      }
    `
    
    const resolvers = {
      Query: {
        moments () {
          return moments
        }
      },
      Mutation: {
        addComment (_, { entity }, unknown, context) {
          console.log(entity)
          moments[0].comments.push(entity)
          return entity
        }
      }
    }
    
    const schema = makeExecutableSchema({
      typeDefs,
      resolvers
    })
    
    // koaBody is needed just for POST.
    router.post('/graphql', koaBody(), graphqlKoa({ schema: schema }))
    // router.get('/graphql', graphqlKoa({ schema: schema }))
    
    router.get('/graphiql', graphiqlKoa({ endpointURL: '/graphql' }))
    
    async function responseMiddleware(ctx, next) {
      ctx.set('Access-Control-Allow-Origin', 'http://localhost:8080')
      ctx.set('Access-Control-Allow-Methods', 'POST,OPTIONS')
      ctx.set('Access-Control-Allow-Headers', 'authorization,content-type')
      // ctx.set('Access-Control-Allow-Credentials', 'true')
      await next()
    }
    
    app.use(responseMiddleware)
    app.use(router.routes())
    app.use(router.allowedMethods())
    
    app.ws.use(responseMiddleware)
    app.ws.use(router.routes())
    app.ws.use(router.allowedMethods())
    
    app.listen(PORT)
    

GraphQL概述
=========

### GraphQL基本语法特性

包括有fields,alias,arguments,fragments,variables,directives,inline fragments

*   field

### GraphQL类型系统

主要由RootQuery + RootMutation两种入口类型（操作）加上RootValue（resolvers）构成GraphQL Schema。（此处用graphql-tools是为了将所有的类型定义在一个字符串中，后续会移到一个.graphql文件中，然后用graphql-import导入）

### GraphQL类型系统内置基础类型

*   标量类型（Scalar Types）
    *   `Int`: 有符号的32位整数
    *   `Float`: 有符号双精度浮点值
    *   `String`: UTF-8字符序列
    *   `Boolean`: true or false
    *   `ID`：ID 标量类型表示一个唯一标识符（类似一种UUID），通常用以重新获取对象或者作为缓存中的键。ID 类型使用和 String 一样的方式序列化。
*   枚举类型（Enumeration Types）
    

是一种特殊的标量类型

    enum Episode {
      NEWHOPE
      EMPIRE
      JEDI
    }
    

*   数组类型（Array Types）

用方括号`[]`标记列表

*   接口类型（Interface Types）

是一种抽象类型，与java的interface机制类似。

*   联合类型（Union Types）

    {
      search(text: "an") {
        ... on Human {
          name
          height
        }
        ... on Droid {
          name
          primaryFunction
        }
        ... on Starship {
          name
          length
        }
      }
    }
    

*   输入类型（Input Types)

与之前提到的所有Types对立，这是一种也是唯一一种输入类型，其主要用于`mutations`时传递`整个对象`的case，它没有参数。

### 内置修饰符

*   `!`: 表示非空。如下

    query DroidById($id: ID!) {
      droid(id: $id) {
        name
      }
    }
    

### GraphQL工作原理

GraphQL中每个查询字段是返回子类型的父类型函数。每个类型的字段对应由一个`resolver`函数支持，当字段被执行时，响应的`resolver`被调用并return结果。 如果该字段为`标量类型`值，比如字符串或数字，则执行完成。否则`递归`执行对应解析器直至结果为`标量类型`值。

### GraphQL基本数据流

每个GraphQL服务端应用的顶层必定会有一个`入口点`，通常为Root或者Query类型，接着执行该字段预设的`解析器`（同步或异步），而每个字段被解析的结果被放置在键值映射中，字段名（或别名）作为键，解析器的值作为值，这个过程从查询字段的`底部叶子节点`开始返回，直到Query类型的`起始节点`，最后生成`镜像查询结果`返回给客户端

Vue工程接入GraphQL
==============

安装vue-cli3.x

    npm i -g @vue/cli
    

初始化工程

    vue create [project-name]
    

引入apollo插件

    cd [project-name]
    vue add apollo
    

FriendCircle.vue

    <template>
      <div>
        <div v-for="(item, index) in moments" :key="index">
          { {item}}
        </div>
        <input type="text" v-model="comment.reply" placeholder="请输入要回复的内容">
        <button @click="addComment">回复</button>
      </div>
    </template>
    
    <script>
    import gql from 'graphql-tag'
    
    const QUERY_LIST = gql`
      query {
        moments {
          user {
            id
            name
            avatar
          }
          main {
            content
            pics
          }
          comments {
            user {
              id
              name
            }
            reply
          }
        }
      }
    `
    
    export default {
      data () {
        return {
          moments: [],
          comment: {
            user: {
              id: (Math.random() * 10000).toFixed(0),
              name: '费德提克'
            },
            reply: ''
          }
        }
      },
      apollo: {
        moments: {
          query: QUERY_LIST
        }
      },
      methods: {
        addComment () {
          this.$apollo.mutate({
            mutation: gql`
              mutation addComment($comment: Add_Comment) {
                addComment(entity: $comment) {
                  user {
                    id
                    name
                  }
                  reply
                }
              }
            `,
            variables: {
              comment: this.comment
            },
            update: (store, { data: { addComment } }) => {
              // Read the data from our cache for this query.
              const data = store.readQuery({ query: QUERY_LIST })
              // set first moment's comment
              data.moments[0].comments.push(addComment)
              // Write our data back to the cache.
              store.writeQuery({ query: QUERY_LIST, data })
            }
          })
        }
      }
    }
    </script>
    

涉及的知识点有Root\_Query,Root\_Mutation,variables以及store cache

### cache

其核心机制包括以下两点

1.  对所有（包括嵌套的）非标量类型递归进行缓存，往往通过类型id或\_id以及\_\_typename唯一组合标识，然后在一个扁平的数据结构中存储
2.  可以设置不同缓存策略：cache-and-network,no-cache,network-only

update回调
========

this.$apollo.mutate(options) options中有一个update回调，在成功响应数据后触发，并且可以直接读取并操作由`apollo-cache-inmemory`生成的store。上述例子中使用此回调同步更新缓存以及UI

> 注：所有绑定的变量均不可直接修改，内部使用Object.freeze将对象冻结，无法直接增删。