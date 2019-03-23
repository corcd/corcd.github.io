---
layout:       post
title:        "ECMAScript 学习记录"
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
##### `let` 
- 严格限制作用域，无法在作用域外生效；可有效避免变量提升而导致的污染
- 暂时性死区 --> 意味着typeof不再是一个百分之百安全的操作
- 不允许在相同作用域内，重复声明同一个变量
- 参数作用域会带入函数体，所以参数已经声明的变量，函数体内不得用let再次声明

##### `const`
- 声明一个只读的常量，一旦声明，常量的值就不能改变
- 对于const来说，只声明不赋值，就会报错
- 同样存在暂时性死区，只能在声明的位置后面使用

实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心

> 即，可以定义一个对象为常量，不能更改指向另一个对象，但是可以为该常量对象添加属性（实际上已经改变了常量）

##### 顶层对象
顶层对象，在浏览器环境指的是 window 对象，在 Node 指的是 global 对象

ES5 之中，顶层对象的属性与全局变量是等价的
```
window.a = 1;
a // 1

a = 2;
window.a // 2
```
- 为了保持兼容性，var 命令和 function 命令声明的全局变量，依旧是顶层对象的属性
- let 命令、const 命令、class 命令声明的全局变量，不属于顶层对象的属性

### 变量解构
##### 基本解构
“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值：
```
let [a, b, c] = [1, 2, 3];
let [foo, [[bar], baz]] = [1, [[2], 3]];
```
> 如果解构不成功，变量的值就等于 undefined

##### 不完全解构
等号左边的模式，只匹配一部分的等号右边的数组，这种情况下，解构依然可以成功（不完全部分匹配右侧第一个）
```
let [x, y] = [1, 2, 3];
x // 1
y // 2

let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
```
> Set 结构也相同

##### 默认值
解构赋值允许指定默认值
```
let [foo = true] = [];
foo // true

let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
```
ES6 内部使用严格相等运算符（===），判断一个位置是否有值，只有当一个数组成员严格等于 undefined，默认值才会生效

> 如果一个数组成员是 null，默认值就不会生效，因为 null 不严格等于 undefined
> 如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值

##### 对象的解构赋值
解构不仅可以用于数组，还可以用于对象
```
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
```
对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值

如果变量名与属性名不一致时：
```
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```

```
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
```
此时，p 是模式，不会被赋值；如果p也要作为变量赋值,应为`let { p, p: [x, { y }] } = obj;`

由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构
```
let arr = [1, 2, 3];
let {0 : first, [arr.length - 1] : last} = arr;
first // 1
last // 3
```

##### 字符串的解构赋值
字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象
```
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```
类似数组的对象都有一个 length 属性，因此还可以对这个属性解构赋值
```
let {length : len} = 'hello';
len // 5
```

##### 数值和布尔值的解构赋值
解构赋值时，如果等号右边是数值和布尔值，则会先转为对象
```
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```
> 解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于 undefined 和 null 无法转为对象，所以对它们进行解构赋值，都会报错

##### 函数参数的解构赋值
函数的参数也可以使用解构赋值。
```
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3
```
> 上面代码中，函数 add 的参数表面上是一个数组，但在传入参数的那一刻，数组参数就被解构成变量 x 和 y。对于函数内部的代码来说，它们能感受到的参数就是 x 和 y

> 函数参数的解构也可以使用默认值，解构失败时（为 undefined 触发），x 和 y 等于默认值

### 数组的扩展
##### （...）运算符

### 函数的扩展

### Symbol 类型
ES6中新增加的原始数据类型

### Set 和 Map 数据结构

### Proxy

### Reflect

### Promise 对象


##### then 方法
then 方法中回调函数参数为 Promise 对象 resolved/rejected 方法传回的参数

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