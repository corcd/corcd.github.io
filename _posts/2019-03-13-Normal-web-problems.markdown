---
layout:       post
title:        "常见的前端应用场景问题"
subtitle:     "积累的多个在前端开发中常常遇到的应用型问题"
date:         2019-03-13
author:       "corcd"
header-img:   null
header-mask:  0.05
catalog:      true
tags:
    - 技术
    - 前端
    - JavaScript
---
# JS 业务相关
### Ajax 传递中文乱码问题
1. js 文件中使用 `encodeURI()` 方法，在后台中对传递的参数进行 `URLDecoder` 解码

```js
前端 -> 后端
encodeURI(text) //通过 Ajax 数据传递：只需编码一次；通过 URL 传递：需要编码两次
URLDecoder.decode(query,"utf-8") //都只反编码一次即可

后端 -> 前端
URLEncoder.encode("中文内容","utf-8") //只需对中文的内容进行编码，如果对整个 json 字符串进行编码，json 中的“”等字符也会被编码导致前端解析json错误
decodeURI(data.CN) //对应的也只需要对中文进行反编码
```

2. 统一使用 UTF-8 编码

### 不借助第三个变量交换 A 和 B
1. 思路：都是让其中一个变量变成一个 a 和 b 都有关系的值，这样可以先改变另一个变量值，最后改变原修改的变量值 

```js
a = a + b
b = a - b
a = a - b
```

2. 思路：通过底层位运算来进行交换变量值 

```js
a ^= b
b ^= a
a ^= b

a = (b^=a^=b)^a; //简化
```

3. 思路：先将 a 变成一个对象，这个对象保存着应该交换后的键值对，最后赋值

```js
a = {a:b,b:a}
b = a.b
a = a.a
```


4. 思路：和前一个方法类似，只不过对象换成了数组

```js
a = [a,b]
b = a[0]
a = a[1]
```


5. 思路：根据运算符优先级，首先执行 b=a，此时的 b 直接得到了 a 的变量值，然后一步数组索引让 a 得到了 b 的值（妙啊）

```js
a = [b,b=a][0]
```

6. ES6 解构赋值

```js
[a,b] = [b,a]
```

### Vue 首屏加载优化
1. 按需加载模块

2. 基于 DllPlugin 和 DllReferencePlugin 的 webpack 构建优化
DllPlugin 预编译模块，对项目中基本不变的、固定的模块进行预编译

3. 异步按需加载组件

```js
// before:
import search from './search.vue'
{
    path: '/search',
    name: 'search',
    component: search
}

// after
const search = resolve => require(['./search.vue'], resolve);
{
    path: '/search',
    name: 'search',
    component: search
}
```

4. 优化组件加载时机
将组件注册事件写在需要使用组件之前

5. webpack-bundle-analyzer
`npm run build --report` 查看依赖关系，然后再根据具体情况划分代码块

6. 服务器端 gzip

### 不可见缓存图片
##### 思路————预加载：
常用的是`new Image();`设置其 src 来实现预载，再使用`onload()`方法回调预加载完成事件。只要浏览器把图片下载到本地，src 就会使用缓存，这是最基本的预加载方法；当 image 下载完图片后，会得到宽和高，因此可以在加载前得到图片的大小

```js
function loadImage(url,callback) {
    var img = new Image();
    
    img.src = url;
 
    if(img.complete) {  // 如果图片已经存在于浏览器缓存，直接调用回调函数
        
        callback.call(img);
        return; // 直接返回，不用再处理onload事件
    }
 
    img.onload = function(){
        img.onload = null;
        callback.call(img);
    }
}
```

##### 其他方法：
- 单纯的 CSS 实现
可通过 CSS 的 background 属性将图片预加载到屏幕外的背景上。只要这些图片的路径保持不变，当它们在 Web 页面的其他地方被调用时，浏览器就会在渲染过程中使用预加载（缓存）的图片。简单、高效，不需要任何 JavaScript

```js
#preload-01 { background: url(http://domain.tld/image-01.png) no-repeat -9999px -9999px; } 
```

- 单纯的 JS 预加载图片

```js
<div class="hidden">  
    <script type="text/javascript">  
        <!--//--><![CDATA[//><!--  
            var images = new Array()  
            function preload() {  
                for (i = 0; i < preload.arguments.length; i++) {  
                    images[i] = new Image()  
                    images[i].src = preload.arguments[i]  
                }  
            }  
            preload(  
                "http://domain.tld/gallery/image-001.jpg",  
                "http://domain.tld/gallery/image-002.jpg",  
                "http://domain.tld/gallery/image-003.jpg"  
            )  
        //--><!]]>  
    </script>  
</div>
```

- 使用 Ajax 实现预加载

```js
window.onload = function() {  
    setTimeout(function() {  
        // XHR to request a JS and a CSS  
        var xhr = new XMLHttpRequest();  
        xhr.open('GET', 'http://domain.tld/preload.js');  
        xhr.send('');  
        xhr = new XMLHttpRequest();  
        xhr.open('GET', 'http://domain.tld/preload.css');  
        xhr.send('');  
        // preload image  
        new Image().src = "http://domain.tld/preload.png";  
    }, 1000);  
};
```

### 隐藏滚动条
- 计算滚动条宽度并隐藏起来

- 使用三个容器包围起来，不需要计算滚动条的宽度
将内容限制在盒子里面了，看不到滚动条同时也可以滚动

- css 隐藏滚动条

```css
//Chrome 和 Safari
.element::-webkit-scrollbar { width: 0 !important }

//IE 10+
.element { -ms-overflow-style: none; }

//Firefox
.element { overflow: -moz-scrollbars-none; }
```

# 前端理论
### 为什么叫层叠样式表
一个标签的样式可以被多个选择器控制，而不同选择器给出的样式会有不同也会有相同样式属性的冲突，于是就会有优先级问题，以及样式的叠加与覆盖；层叠、级联，即是样式的处理方式

### 常见的异步编程模型
- 回调函数
黑盒情况下无从得知 callback 会几时调用、在什么情况下调用，代码间产生了一定的耦合，流程上也会产生一定的混乱

- 事件监听
采用事件驱动，执行顺序取决于事件顺序；通过事件解耦，但流程顺序更加混乱

- 链式异步
解决了异步编程模型的执行流程不清晰的问题，在链条越后位置的方法就越后执行。`$(document).ready`就是典型，DOMCotentLoaded 是一个事件，在 DOM 并未加载前，jQuery 的大部分操作都不会奏效，但 jQuery 的设计者并没有把他当成事件一样来处理，而是转成一种“选其对象，对其操作”的思路

- Deferred & Promise
CommonJS 中的异步编程模型也延续了这一想法，每一个异步任务返回一个 Promise 对象，该对象有一个 then 方法，允许指定回调函数

```js
f().then(f2).then(f3);
```

### 什么是前端工程化
要解决前端工程化的问题，可以从两个角度入手：开发和部署。

从开发角度，要解决的问题包括：

- 提高开发生产效率
- 降低维护难度

这两个问题的解决方案有两点：
- 制定开发规范，提高团队协作能力；
- 分治（软件工程中有个很重要的概念叫做模块化开发其中心思想就是分治）

从部署角度，要解决的问题主要是资源管理，包括：
- 代码审查
- 压缩打包
- 增量更新
- 单元测试

要解决上述问题，需要引入构建/编译阶段

1. 开发规范
开发规范的目的是统一团队成员的编码规范，便于团队协作和代码维护。开发规范没有统一的标准，每个团队可以建立自己的一套规范体系。

值得一提的是JavaScript的开发规范，尤其是在ES2015越来越普及的局面下，保持良好的编码风格是非常必要的。笔者推荐Airbnb的eslint规范。


2. 模块/组件化开发的必要性
随着web应用规模越来越大，模块/组件化开发的需求就显得越来越迫切。模块/组件化开发的核心思想是分治，主要针对的是开发和维护阶段。

关于组件化开发的讨论和实践，业界有很多同行做了非常详细的介绍，本文的重点并非关注组件化开发的详细方案，便不再赘述了。笔者收集了一些资料可供参考：

Web应用的组件化开发；
前端组件化开发实践;
大规模的前端组件化与模块化。

3. 构建&编译
严谨地讲，构建（build）和编译（compile）是完全不一样的两个概念。两者的颗粒度不同，compile面对的是单文件的编译，build是建立在compile的基础上，对全部文件进行编译。在很多Java IDE中还有另外一个概念：make。make也是建立在compile的基础上，但是只会编译有改动的文件，以提高生产效率。本文不探讨build、compile、make的深层运行机制，下文所述的前段工程化中构建&编译阶段简称为构建阶段。

### get 请求传参长度的误区
> 误区：我们经常说 get 请求参数的大小存在限制，而 post 请求的参数大小是无限制的

**实际上 HTTP 协议从未规定 GET/POST 的请求长度限制是多少。**对 get 请求参数的限制是来源与浏览器或 web 服务器，浏览器或 web 服务器限制了 url 的长度。为了明确这个概念，我们必须再次强调下面几点:
- 不同的浏览器和 web 服务器，限制的最大长度不一样
- **要支持IE，则最大长度为 2083byte，若只支持 Chrome，则最大长度 8182byte**


# jQuery 相关
### jQuery 中 attr() 和 prop() 原理上的区别
`prop()` 是针对 Dom 元素属性，`attr()` 针对 HTML 元素属性；`attr()` 是通过 `setAtrribute()` 和 `getAttribute()` 来设置的，使用的是 DOM 属性节点，而 `prop()` 是通过 `document.getElementById(el)[name] = value` 来设置的，是转化为 JS 对象的属性。

##### Attribute 和 Property 的区别
- Attribute 就是 DOM 节点自带属性，例如我们在 HTML 中常用的 id,class,src,title,alt 等，也可以是用户添加的自定义属性

- 而 Property 则是这个 DOM 元素作为对象，其附加的属性或者内容，例如 childNodes，firstChild 等

##### 两者取值和赋值的区别
- Attribute 取值和赋值

```js
//attribute 取值
  getAttribute(attribute)
  eg:var id = div1.getAttribute('id')
     var id = div1.getAttribute('title1')
//attribute 赋值
  setAttribute(attribute,value)  //value 只能是字符串形式
  eg:div1.setAttribute('class', 'a')
     div1.setAttribute('title1', 'asd')  //也可以是自定义属性
```

- Property 取值和赋值

```js
//通过'.'号获取 property
  var id = div1.id;
  var className = div1.className; //相当于div1.getAttribute('class')
//通过'='赋予 property
  div1.className = 'a';
  div1.align = 'center';
```

##### attr() 和 prop() 区别
- 针对属性对象不同

`prop()` 是针对 Dom 元素属性，`attr()` 针对 HTML 元素属性，和 attribute 与 property 区别一样

- 用于设置的属性值类型不同

`attr()` 函数操作的是文档节点的属性，因此**设置的属性值只能是字符串类型**，如果不是字符串类型，也会调用其 `toString()` 方法，将其转为字符串类型

`prop()` 函数操作的是 JS 对象的属性，因此设置的属性值可以为包括数组和对象在内的任意类型

- 应用版本不同

`attr()` 是 jQuery 1.0版本就有的函数，`prop()` 是 jQuery 1.6版本新增的函数。毫无疑问，在1.6之前，你只能使用 `attr()` 函数；1.6及以后版本，你可以根据实际需要选择对应的函数

- 其他不同

对于表单元素的 checked、selected、disabled 等属性，在 jQuery 1.6之前，两个的返回结果一样都是 Boolean 类型，但是从1.6开始，使用 `attr()` 获取这些属性的返回值为 String 类型，如果被选中(或禁用)就返回 checked、selected 或 disabled，否则(即元素节点没有该属性)返回 undefined。并且，在某些版本中，这些属性值表示文档加载时的初始状态值，即使之后更改了这些元素的选中(或禁用)状态，对应的属性值也不会发生改变。

### jQuery 中使用 on() 可以绑定多个函数到同一事件，且不会被覆盖
用 `addEventListener()` 同样可以同一种类型的事件注册多个事件句柄（有别于 onXXX 注册方式），但是 jQuery 内部并没有采用这个方法，为了兼容所有的浏览器和实现事件的委托，设计的极为复杂，使用**数组的方式来存放事件处理函数**，触发事件时遍历这个数组执行

### jQuery 实现链式调用的原理
原理很简单，在 jQuery 对象 prototype 的方法上，最后会通过`return this`**返回对象**，就可以继续调用对象方法
> 目的是，节省代码量，使代码看起来更优雅

### $(document).ready() 相关
`ready()` 函数用于在文档进入 ready 状态时执行代码；当 DOM 完全加载（例如 HTML 被完全解析 DOM 树构建完成时），jQuery允许你执行代码（有别于 `window.onload` 事件，onload 需要页面资源完全加载时触发）；使用 `$(document).ready()` 的最大好处在于它适用于所有浏览器，另一个优势是你可以在网页里多次使用它，浏览器会按它们在 HTML 页面里出现的顺序执行它们，相反对于 onload 而言，只能在单一函数里使用

### jQuery 中 detach() 和 remove() 方法的区别
尽管 `detach()` （detach：分离）和 `remove()` （remove：移除）方法都被用来移除一个 DOM 元素, 两者之间的主要不同在于 `detach()` 会保持对过去被解除元素的跟踪, 因此它可以被取消解除, 而 `remove()` 方法则会保持过去被移除对象的引用

### JQuery 阻止事件冒泡的两种方式

方式一：`event.stopPropagation()`
```js
$("#div1").mousedown(function(event){
  event.stopPropagation();
});
```
方式二：`return false`
```js
$("#div1").mousedown(function(event){
  return false;
});
```
区别：
- `return false` 不仅阻止了事件往上冒泡，而且阻止了事件本身
- `event.stopPropagation()` 则只阻止事件往上冒泡，不阻止事件本身

# 高效算法
### Top K 问题


### Two Sum 问题
```js
var twoSum = function(nums, target){
  if ( !Array.isArray(nums) || Object.prototype.toString.call(target) !== "[object Number]" ) return;

  var arr = [],
      i,
      j,
      len = nums.length;

  for ( i = 0; i < len; i ++ ){
    j = arr[ target - nums[i] ];
    if ( j !== undefined  ) return [ j, i ];
    arr[nums[i]] = i;
  }
}
```

### N Sum 问题

# npm 相关
### npm 全局安装 & 本地安装、开发依赖和生产依赖
##### 全局安装：
`npm install <pageName> -g  //（这里-g是-global的简写）`
通过上面的命令行（带-g修饰符）安装某个包，就叫全局安装。通常全局包安装在**node 目录下的 node_modules 文件夹**。可以通过执行下面几条命令查看 node、npm 的安装目录和全局包的安装目录。
```shell
which node   // 查看 node 的安装目录
which npm   // 查看 npm 的安装目录
npm root -g // 查看全局包的安装目录
npm list -g --depth 0 //查看全局安装过的包
```

##### 本地安装:
`npm install <pageName> (后面可以加几种修饰符，主要有两种--save-dev和--save)`
通过上面的命令行安装某个包，就叫本地安装。包安装在你**当前项目文件夹**下的 node_modules 文件夹中。

三、全局安装的作用:

全局安装的包可提供直接执行的命令(例：gulp -h可以查看gulp定义了什么命令)。 比如gulp全局安装后，可以在命令行上直接执行gulp -v、gulp -h等（原理：全局安装的gulp会将其package.json中的bin命令注入到了全局环境，使得你可以全局执行：gulp xxx命令，这另一个话题了，不深入）。倘若只在本地安装了gulp，未在全局安装gulp，直接执行这些命令会报错。你想要执行相应的命令则可能需要例如：node ./node_modules/gulp/bin/gulp.js -v(查看版本) 这样用一大串命令来执行。因此全局安装就发挥到他的好处了，一个gulp -v就搞定

当然，不是每个包都必须要全局安装的，一般在项目中需要用到该包定义的命令才需要全局安装。比如gulp <taskName>执行gulp任务...等，所以是否需要全局安装取决于我们如何使用这个包。全局安装的就像全局变量有点粗糙，但在某些情况下也是必要的，全局包很重要，但如果不需要，最好避免使用。

四、可以全局安装，那么直接全局安装到处使用就行了，干嘛还需要本地安装？

如果只是全局安装了而没本地安装，就得require('<pagePath>') 例：引入一个全局的包可能就是requirt('/usr/local/....')通过全局包的路径引入，这样显然十分的不灵活。如果安装了本地包，那么就可以直接require('<pageName>')引入使用。
一个包通常会在不同的项目上会重复用到，如果只全局安装，那么当某个项目需要该包更新版本时，更新后可能就会影响到其他同样引用该包的项目，因此本地安装可以更灵活地在不同的项目使用不同版本的包，并**避免全局包污染**的问题

> 经验法则：要用到该包的命令执行任务的就需要全局安装，要通过 require 引入使用的就需要本地安装（ 但实际开发过程中，我们也不怎么需要考虑某个包是全局安装还是本地安装，因为这一点在该包的官网上一般会明确指出，以上是为了理解全局安装和本地安装）

开发依赖和生产依赖
顺着上面讲到的本地安装，本地安装有两种主要的安装方式：

保存到开发依赖(devDependencies): npm install <pageName> --save-dev(等同 npm install <pageName>)
保存到生产依赖(dependencies): npm install <pageName> --save
"开发依赖"顾名思义在开发环境中用到的依赖，"生产依赖"在生产环境中用到的依赖。那么这里又延伸出个问题什么是开发环境、什么是生产环境？

一、开发环境和生产环境

【开发环境】：指的是你的项目尚且在编码阶段时的环境。你在代码可能还有各种console.log()、注释、格式化等。
【生产环境】：指的是你的项目已经完成编码，并发布上线可供用户浏览的阶段时的环境。代码可能经过了压缩、优化等处理。

这些概念其实并没有一个很明确的定义，接下来我们举例个场景，将"开发环境"、"生产环境"和上面的"开发依赖"、"生产依赖"联系起来就会比较容易理解的了。假如我们在开发过程中使用jQuery。在以往，可能就是把jQuery这个插件下载的本地，再通<script>引入html中。但这有个不方便的地方，我们每次进行一个项目的时候就得手动复制这个jQuery文件到我们的项目中，如果想要换个版本又得官网上下载、随着项目越来越多。用到的插件、库也随之越繁杂...这样会造成自家用的插件管理繁琐的问题。因此就出现了npm(包管理工具)你需要用到什么，直接通过一条命令行就可以将想要的插件下载下来，并直接引入到项目中，目前几乎所有的js插件都能在npm上直接下载。

二、生产依赖

回到环境和依赖话题，我们下载的jQuery，在开发时参与源码编写，在发布上线的生产环境中也是需要它的。不仅在开发环境编写代码时要依赖它、线上环境也要依赖它，因此将它归类为"生产依赖"，安装时执行npm install jquery --save，它就会被记录在package.json的dependencies。当进行代码打包时，会将这里的jQuery打包入我们的项目代码中。

三、开发依赖

接着，假如我们用gulp对html进行压缩，我们通常会用到一个插件gulp-htmlmin。我们只希望它把html压缩完就ok了，并不希望它融入我们的项目代码中，即只存在于开发环境，因此把他归类为"开发依赖"。安装时执行npm install gulp-htmlmin --save-dev它就会被记录在package.json的devependencies下，当进行代码打包时，不会将这里的gulp-htmlmin插件源码打包入我们的项目代码中

devDependencies只会在开发环境下使用，生产环境不会被打入包内；而dependencies不仅在开发环境中要使用，生产环境也需要使用到。根据以上规则，我们就很容易区分哪些插件是用--save-dev模式安装，哪些用--save模式安装。

##### 来源
引用自[炮哥炮炮-npm(你怕吗) 全局安装与本地安装、开发依赖和生产依赖](https://segmentfault.com/a/1190000017787636)

