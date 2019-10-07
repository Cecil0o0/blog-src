---
title: 概述nodejs模块系统核心原理
tags:
  - module
  - nodejs
  - 循环引用
  - 模块系统
  - 模块缓存
url: 898.html
id: 898
categories:
  - technology
date: 2018-06-23 16:25:07
---

- 核心工作原理
  - 模块解析过程（Module Resolve）
  - 模块对象（Module Object）
  - 模块包装（Module Wrapper）
  - 缓存（Cache）
  - 循环引用（Cycle）

核心工作原理

======

## 模块解析过程（Module Resolve）

```js
require("x");
```

1. x 是 node 核心模块（如 http,zlib 等）则返回，否则继续
2. 根据 Module 对象的 paths 属性一直递归找`node_modules`文件夹下是否存在该模块，直到根目录，否则抛出 Error('MODULE_NOT_FOUND')
3. x 是路径（如/path/to/file）
   3.1 尝试 LOAD_AS_FILE（如.js,.json,.node），没有则继续
   3.2 尝试 LOAD_AS_DIR（如文件夹下 package.json），没有则抛出 Error('MODULE_NOT_FOUND')

LOAD_AS_FILE:

1. `.js`
2. `.json`
3. `.node`文件（编译好的二进制 node 插件）

LOAD_AS_DIR:

1. `X/package.json`中的 main 字段作为模块入口文件
2. `index.js`
3. `index.json`
4. `index.node`

模块对象

---

```text
    Module {
      id: '.',
      exports: {},
      parent: null,
      filename: '/Users/wl/Sites/myapp/node-learning/src/module/index.js',
      loaded: false,
      children:
       [
           Module
           {
               id: '/Users/wl/Sites/myapp/node-learning/src/module/a.js',
               exports: [Object],
               parent: [Circular],
               filename: '/Users/wl/Sites/myapp/node-learning/src/module/a.js',
               loaded: true,
               children: [Array],
               paths: [Array]
            }
        ],
      paths:
       [
            '/Users/wl/Sites/myapp/node-learning/src/module/node_modules',
             '/Users/wl/Sites/myapp/node-learning/src/node_modules',
             '/Users/wl/Sites/myapp/node-learning/node_modules',
             '/Users/wl/Sites/myapp/node_modules',
             '/Users/wl/Sites/node_modules',
             '/Users/wl/node_modules',
             '/Users/node_modules',
             '/node_modules'
       ]
    }
```

- `id` 模块 id。通常为模块文件`绝对`路径，主模块通常为`.`
- `exports` 模块导出对象。这里的 exports 指的是 module.exports
- `parent` 父模块。即被依赖模块
- `filename` 模块文件名。通常与`id`相同
- `loaded` 模块加载状态。是否已执行完成
- `children` 子模块。即依赖模块
- `paths` 模块查找路径数组。

## 模块包装（Module Wrapper）

主要由以下两点考虑

- 使模块内定义的顶层变量限制在方法（也就是 wrapper 或者说模块内部）级作用域中，防止污染 global 环境

> 注：建议启用'use strict'模式，防止定义全局变量

- 传入`module`, `require`有利于实现 node 模块化机制

```js
(function (exports, require, module, __filename, __dirname) {
  // Module code
});
```

## 缓存（Cache）

在一个 node 上下文环境中，两次`require`同一个文件，`通常`情况下返回完全相同的两个对象引用。除非用高阶函数返回工厂函数。

## 循环引用（Cycle）

由于 node 包相互依赖，则较大可能会形成`循环引用`，node 利用其`缓存`机制避免无限循环。比如 `index.js`

```js
const prefix = "主模块：";
const a = require("./a.js");
console.log(prefix, a); // {a:2}
console.log(prefix, require.main === module);
console.log(module);
```

`a.js`

```js
"use strict";
const prefix = "A模块：";
module.exports = { a: 1 };
const b = require("./b.js");
console.log(prefix, b); // {b:1}
module.exports = { a: 2 };
console.log(prefix, require.main === module);
```

`b.js`

```js
const prefix = "b模块：";
module.exports = { b: 1 };
const a = require("./a.js");
console.log(prefix, a); // {a:1}
console.log(prefix, require.main === module);
```

如上。当 b.js 引用 a.js 时，为避免无限循环，a.js`未完成的副本`（我认为是 require('./b.js')之前的所有代码，但是在官方文档中未得到特别确切的表述）导出的对象被 b.js 引用
