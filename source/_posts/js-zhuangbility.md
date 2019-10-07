---
title: JS zhuangbility
tags:
  - js
  - zhuangbility
  - 简写
  - 装逼
url: 229.html
id: 229
categories:
  - technology
date: 2017-08-21 22:40:34
---

1.  展示页面中所有元素

    [].forEach.call($$('*'),function(dom){dom.style.outline="1px solid #" + (~~(Math.random()*(1<<24))).toString(16)})
    

1.  JS错误处理正确姿势

    try {
        // do something
    } catch (e) {
        window.location.href = "https://stackoverflow.com/search?q=[js]+"+ e.message;
    }
    

1.  单行代码写评星组件

    let rate = 1
    "★★★★★☆☆☆☆☆".slice(5 - rate, 10 - rate)
    

1.  如何装逼用代码骂人SB

    (!(~+[])+{})[--[~+""][+[]]*[~+[]] + ~~!+[]]+({}+[])[[~!+[]]*~+[]]
    

1.  如何用代码优雅的证明自己NB

    ([][[]]+[])[+!![]]+([]+{})[!+[]+!![]]
    

1.  如何优雅的取随机数

    Math.random().toString(16).substring(2) // 13位
    Math.random().toString(36).substring(2) // 11位
    

1.  如何优雅的取整

    var a = ~~2.332
    var b= 2.33 | 0
    var c= 2.33 >> 0
    

1.  如何优雅实现金钱格式化

    var test1 = '1234567890'
    var format = test1.replace(/\B(?=(\d{3})+(?!\d))/g, ',')
    
    console.log(format) // 1,234,567,890
    

1.  逗号运算符

    var a = 0; 
    var b = ( a++, 99 ); 
    console.log(a);  // 1
    console.log(b);  // 99
    

1.  标准JSON实现深拷贝(会忽略undefined和function)

    var a = {
        a: '1',
        b: {
            ss: '123'
        }
    }
    var b = JSON.parse(JSON.stringify(a))
    

1.  parseInt(0.0000008) === 8
    
2.  最短的代码实现数组去重
    

    [...new Set([1, "1", 2, 1, 1, 3])]
    

1.  用最短的代码实现一个长度为m(6)且值都n(8)的数组

    Array(6).fill(8)
    

1.  如何优雅的实现强制参数?

    let mandatory = () => {return new Error('Missing Parameter')}
    
    let foo = (bar = mandatory()) => {return bar}
    

1.  如何优雅的可复用的校验模式？

    let schema = {
        name: {
            required: true
        }
    }
    
    let validator = (schema, values) => {
        for(let field in schema) {
            if (schema[field].required) {
                if (!values[field]) {
                    return false
                }
            }
        }
    }
    
    // 使用方法
    validator(schema, { name: 'Cecil' })// return true