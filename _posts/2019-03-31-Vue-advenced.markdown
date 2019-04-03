---
layout:       post
title:        "Vue 进阶"
subtitle:     "深入理解 Vue"
date:         2019-03-31
author:       "corcd"
header-img:   null
header-mask:  0.05
catalog:      true
tags:
    - 技术
    - 前端
    - JavaScript
    - Vue
---
### Vue 实现数据双向绑定的原理 Object.defineProperty()

数据劫持结合发布者-订阅者模式

通过`Object.defineProperty()`来劫持各个属性的 setter，getter，在数据变动时发布消息给订阅者，触发相应监听回调

> 当把一个普通 Javascript 对象传给 Vue 实例来作为它的 data 选项时，Vue 将遍历它的属性，用 `Object.defineProperty()` 将它们转为 getter/setter，虽然用户看不到 getter/setter，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化

vue 的数据双向绑定 将 MVVM 作为数据绑定的入口，整合 Observer，Compile 和 Watcher 三者，通过 Observer 来监听自己的 model 的数据变化，通过 Compile 来解析编译模板指令（vue 中是用来解析 {{}}），最终利用 watcher 搭起 observer 和 Compile 之间的通信桥梁，达到数据变化 —>视图更新；视图交互变化（input）—>数据 model 变更双向绑定效果

##### JS 实现简单双向绑定：
``` JavaScript
<body>
    <div id="app">
    <input type="text" id="txt">
    <p id="show"></p>
</div>
</body>
<script type="text/javascript">
    var obj = {}
    Object.defineProperty(obj, 'txt', {
        get: function () {
            return obj
        },
        set: function (newValue) {
            document.getElementById('txt').value = newValue
            document.getElementById('show').innerHTML = newValue
        }
    })
    document.addEventListener('keyup', function (e) {
        obj.txt = e.target.value
    })
</script>
```

### Vue 组件间的参数传递
1. 父组件与子组件传值
父组件传给子组件：子组件通过 props 方法接受数据
子组件传给父组件：$emit 方法传递参数

2. 非父子组件间的数据传递，兄弟组件传值
eventBus，就是创建一个事件中心，相当于中转站，可以用它来传递事件和接收事件（项目比较小时，用这个比较合适）

3. VueX

### Vue 的路由实现：hash 模式 和 history模式
- **hash 模式：**
在浏览器中符号“#”，#以及#后面的字符称之为 hash，用`window.location.hash`读取；
特点：hash 虽然在 URL 中，但不被包括在 HTTP 请求中；用来指导浏览器动作，对服务端安全无用，hash 不会重加载页面。
hash 模式下，仅 hash 符号之前的内容会被包含在请求中，如 http://www.xxx.com，因此对于后端来说，即使没有做到对路由的全覆盖，也不会返回 404 错误

- **history 模式：**
history 采用 HTML5 的新特性；且提供了两个新方法：`pushState()`，`replaceState()`可以对浏览器历史记录栈进行修改，以及 popState 事件的监听到状态变更。
history 模式下，前端的 URL 必须和实际向后端发起请求的 URL 一致，如 http://www.xxx.com/items/id。后端如果缺少对 /items/id 的路由处理，将返回 404 错误。Vue-Router 官网里如此描述：“不过这种模式要玩好，还需要后台配置支持……所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面

### vue路由的钩子函数
首页可以控制导航跳转，beforeEach，afterEach 等，一般用于页面 title 的修改，一些需要登录才能调整页面的重定向功能

beforeEach 主要有3个参数 to，from，next：

- to：
route即将进入的目标路由对象，

- from：
route当前导航正要离开的路由

- next：
function一定要调用该方法resolve这个钩子；执行效果依赖next方法的调用参数；可以控制网页的跳转

### VueX
只用来读取的状态集中放在store中； 
改变状态的方式是提交mutations，这是个同步的事物； 
异步逻辑应该封装在action中

导入：在main.js引入store，注入。新建了一个目录store，….. export 

应用场景有：单页应用中，组件之间的状态、音乐播放、登录状态、加入购物车