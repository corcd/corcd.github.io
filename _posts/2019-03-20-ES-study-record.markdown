---
layout:       post
title:        "ES6、ES7 学习记录"
subtitle:     "学习 ECMAScript 标准"
date:         2019-03-20
author:       "corcd"
header-img:   null
header-mask:  0.05
catalog:      true
tags:
    - 技术
    - 前端
    - JavaScript
---

# ES6

### let、const
- `let` 会严格限制作用域，无法在作用域外生效；可有效避免变量污染

- `const`

### 变量解构

### 数组的扩展
##### （...）运算符

### 函数的扩展

### Symbol 类型
ES6中新增加的原始数据类型

### Set 和 Map 数据结构

### Proxy

### Reflect

### Promise 对象

### async 函数

### Class

### Module 

# ES7
### 求幂运算符（**）
```
3 ** 2           // 9
```

效果同：
```
Math.pow(3, 2)   // 9
```

幂等（类似加等）
```
let a = 3
a **= 2
// 9
```

### Array.prototype.includes()
`includes()`的作用，是查找一个值是否在数组里，若存在，则返回 true，反之返回 false。 
基本用法：
```
['a', 'b', 'c'].includes('a')     // true
['a', 'b', 'c'].includes('d')     // false
```

`Array.prototype.includes()`方法接收两个参数：要搜索的值和搜索的开始索引。当第二个参数被传入时，该方法会从索引处开始往后搜索（默认索引值为0）。若搜索值在数组中存在则返回true，否则返回false
```
['a', 'b', 'c', 'd'].includes('b')         // true
['a', 'b', 'c', 'd'].includes('b', 1)      // true
['a', 'b', 'c', 'd'].includes('b', 2)      // false
```

与ES6里数组的另一个方法`indexOf`等效：
```
['a', 'b', 'c'].includes('a')          //true
['a', 'b', 'c'].indexOf('a') > -1      //true
```

> 两者使用的都是 === 操作符来做值的比较。但是`includes()`方法有一点不同，两个 NaN 被认为是相等的，即使在 NaN === NaN 结果是 false 的情况下。这一点和`indexOf()`的行为不同，`indexOf()`严格使用 === 判断

> `includes()`在判断 +0 与 -0 时，被认为是相同的(`indexOf()`也是如此)

> `includes()`只能判断简单类型的数据，对于复杂类型的数据，比如对象类型的数组、二维数组等，这些是无法判断的

# ES8
### 异步函数(Async functions)

为什么要引入async --> 为了避免回调地狱（callback hell）

应对方法：
1. ES6 提出了 Promise对象，它将回调函数的嵌套，改成了链式调用，使用then方法，让异步任务的两段执行更清楚。但是Promise 的最大问题是代码冗余，请求任务多时，一堆的then，也使得原来的语义变得很不清楚
2. 此时引入了另外一种异步编程的机制：Generator；Generator 函数是一个普通函数，但是有两个特征：一是 function 关键字与函数名之间有一个星号；二是函数体内部使用 yield 表达式，定义不同的内部状态
3. 虽然 Generator 将异步操作表示得很简洁，但是流程管理却不方便（即何时执行第一阶段、何时执行第二阶段）。此时，我们便希望能出现一种能自动执行 Generator 函数的方法，即：**async/await**

### Object.entries() & Object.values()


### 字符串填充：padStart & padEnd
padStart 函数通过填充字符串的首部来保证字符串达到固定的长度，反之，padEnd 是填充字符串的尾部来保证字符串的长度的。该方法提供了两个参数：字符串目标长度和填充字段，其中第二个参数可选（默认使用空格填充）

### Object.getOwnPropertyDescriptors()

### 共享内存和原子（Shared memory and atomics）

### 函数参数列表与调用中的尾部逗号
该特性允许我们在定义或者调用函数时添加尾部逗号而不报错

好处：
- 调整结构时，不会因为最后一行代码的位置变动，而去添加或者删除逗号
- 在版本管理上，不会出现因为一个逗号，而使本来只有一行的修改，变成两行

# 统一建议编码风格