---
layout:       post
title:        "高性能 JavaScript"
subtitle:     "脚本的无阻塞加载策略"
date:         2019-03-16
author:       "corcd"
header-img:   null
header-mask:  0.05
catalog:      true
tags:
    - 技术
    - JavaScript
---

## 前言
Javascript 在浏览器中的性能，可以说是前端开发者所要面对的最重要的可用性问题。

在 Yahoo 的 Yslow 23条规则当中，其中一条是将 JS 放在底部 。原因是，事实上，大多数浏览器使用单进程处理 UI 和更新 Javascript 运行等多个任务，而同一时间只能有一个任务被执行。Javascript 运行了多长时间，那么在浏览器空闲下来响应用户交互之前的等待时间就有多长。　

从基本层面说,这意味着`<script>`标签的出现使整个页面因脚本解析、运行而出现等待。不论实际的 JavaScript 代码是内联的还是包含在一个不相干的外部文件中,页面下载和解析过程必须停下,等待脚本 完成这些处理,然后才能继续。这是页面生命周期必不可少的部分,因为脚本可能在运行过程中修改页面 内容。典型的例子是`document.write()`函数,例如:

```
<html>
  <head>
    <title>Script Example</title>
  </head> 
  
  <body>
     <p>
        <script type="text/javascript">
           document.write("The date is " + (new Date()).toDateString());
        </script> 
     </p>
  </body> 
</html>  　
```

当浏览器遇到一个`<script>`标签时,正如上面 HTML 页面中那样,无法预知 JavaScript 是否在`<p>`标签中 添加内容。因此,浏览器停下来,运行此 JavaScript 代码,然后再继续解析、翻译页面。同样的事情发生 在使用 src 属性加载 JavaScript 的过程中。浏览器必须首先下载外部文件的代码,这要占用一些时间,然后 解析并运行此代码。此过程中,页面解析和用户交互是被完全阻塞的。 　　

因为脚本阻塞其他页面资源的下载过程,所以推荐的办法是：将所有`<script>`标签放在尽可能接近`<body>`标签底部的位置,尽量减少对整个页面下载的影响。例如:

```
<html>
 2   <head>
    <title>Script Example</title>
    <link rel="stylesheet" type="text/css" href="styles.css"> 
  </head>
  
  <body>
    <p>Hello world!</p>
    <-- Example of recommended script positioning --> 
      <script type="text/javascript" src="file1.js"></script> 
      <script type="text/javascript" src="file2.js"></script> 
      <script type="text/javascript" src="file3.js"></script>
  </body> 
</html>
```

此代码展示了所推荐的`<script>`标签在 HTML 文件中的位置。尽管脚本下载之间互相阻塞,但页面已经 下载完成并且显示在用户面前了,进入页面的速度不会显得太慢。这就是上面提到的将JS放到底部。

另外，Yahoo! 为他的“Yahoo! 用户接口(Yahoo! User Interface,YUI)”库创建一个“联合句柄”,这是通过他 们的“内容投递网络(Content Delivery Network,CDN)”实现的。任何一个网站可以使用一个“联合句柄” URL 指出包含 YUI 文件包中的哪些文件。例如,下面的 URL 包含两个文件: 
　　
```
<script type="text/javascript" src="http://yui.yahooapis.com/combo?2.7.0/build/yahoo/yahoo-min.js&2.7.0/build/event/event-min.js"></script> 
```

此 URL 调用 2.7.0 版本的 yahoo-min.js 和 event-min.js 文件。这些文件在服务器上是两个分离的文件,但是 当服务器收到此 URL 请求时,两个文件将被合并在一起返回给客户端。通过这种方法,就不再需要两个`<script>`标签(每个标签加载一个文件),一个`<script>`标签就可以加载他们。这是在 HTML 页面包含多个外部 Javascript 的最佳方法。

 

## Noblocking Scripts 非阻塞脚本

上述是页面初始状态包含多个 Javascript 脚本加载的最佳方法。Javascript 倾向于阻塞浏览器某些处理过程，如 http 请求和界面刷新，这是开发者面临的最显著性能问题。保持 Javascript 文件短小，并限制 http 请求的数量，只是创建反应迅速的网页应用第一步。

但诸如大型网页有大量的 JS 代码，保持源码短小并不总是一种最佳选择。所以，非阻塞脚本应运而生，我们需要的是向页面中逐步添加 Javascript，某种程度上而言不会阻塞浏览器。

而非阻塞脚本的关键在于，等页面完成加载之后，再加载 Javascript 源码，这意味着在 window 的 load 事件发出之后开始下载代码。

> 相关解释：　　

- window 的 load 事件只会在页面载入完毕后触发一次且仅一次。
- `window.onload=function(){}`必须等待网页中所有的内容加载完毕后 ( 包括元素的所有关联文件，例如图片 ) 才能执行，即Javascript此时才可以访问页面中的任何元素。

如下述几种方法：

#### Deferred Scripts 延期脚本

HTML4 为`<script>`标签定义了一个扩展属性：defer。

这个 defer 属性指明元素中所包含的脚本不打算修改 DOM，因此代码可以稍后执行。defer 属性只被 Internet Explorer 4+ 和 Firefox 3.5+ 支持，它不是一个理想的跨浏览器解决方案。在其他浏览器上，defer 属性将被忽略。所以，`<script>`标签会按照正常默认方式处理，即是会造成阻塞。如果得到各个主流浏览器的支持，这仍是一种有效的解决方式。

```
<script type="text/javascript" src="file1.js" defer></script>
```

一个带有 defer 属性的`<script>`标签可以放置在文档的任何位置，它会在被解析时启动下载，直到 DOM 加载完成（在 onload 事件句柄被调用之前）。当一个 defer 的 Javascript 文件被下载时，它不会阻塞浏览器的其他处理过程，所以这些文件可以与其他资源一起并行下载。

可以使用下述代码测试浏览器是否支持 defer 属性：

```
<html>
  <head>
    <title>Script Defer Example</title>
  </head> 
 
  <body>
    <script defer> alert("defer");</script> 
    <script> alert("script"); </script> 
    <script> window.onload = function(){ alert("load");}; </script>
  </body> 
</html>
```

如果浏览器不支持 defer，那么弹出的对话框的顺序是“defer”，“script”，“load”。
如果浏览器支持 defer，那么弹出的对话框的顺序是“script”，“load”，“defer”。

　
HTML5 新增了一个 async 属性，对比 defer 与 async 的异同：

> 相同之处：
- 加载文件时不阻塞页面渲染
- 使用这两个属性的脚本中不能调用`document.write()`方法
- 有脚本的 onload 的事件回调

> 区别点：
- HTML 的版本: HTML4 中定义了defer；HTML5 中定义了 async
- 执行时刻：每一个 async 属性的脚本都在它下载结束之后立刻执行，同时会在 window 的 load 事件之前执行。所以就有可能出现脚本执行顺序被打乱的情况；每一个 defer 属性的脚本都是在页面解析完毕之后，按照原本的顺序执行，同时会在 document 的 DOMContentLoaded 之前执行。


#### Dynamic Script Elements 动态脚本元素 

DOM 允许我们使用 Javascript 动态创建 HTML 的几乎所有文档内容，一个新的`<script>`元素可以非常容易的通过标准 DOM 创建：

```
var script = document.createElement ("script");
script.type = "text/javascript";
script.src = "file1.js"; 
document.body.appendChild(script);
```

新的`<script>`元素加载 file1.js 源文件。此文件当元素添加到页面后立刻开始下载。此技术的重点在于：无论在何处启动下载，文件的下载和运行都不会阻塞其他页面处理过程。

当文件使用动态脚本节点下载时，返回的代码通常立即执行（除了 Firefox 和 Opera，它们将等待此前的所有动态脚本节点执行完毕）。

大多数情况下，我们希望调用一个函数就可以实现 Javascript 文件的动态下载。下面的函数封装实现了标准实现和 IE 实现：

```
function loadScript(url, callback){
    var script = document.createElement ("script") ;
    script.type = "text/javascript";
    
    if (script.readyState){ //IE
        script.onreadystatechange = function(){
        if (script.readyState == "loaded" || script.readyState == "complete"){
            script.onreadystatechange = null;
            callback(); 
            }
        };
    } 
    else { //Others
        script.onload = function(){ callback();}; 
    }
    script.src = url;
    document.getElementsByTagName("head")[0].appendChild(script); 
}

loadScript("file1.js", function(){  //调用
    alert("File is loaded!"); 
});
```

此函数接受两个参数：Javascript 文件的 Url 和一个当 Javascript 接收完成时触发的回调函数。属性检查用于决定监视哪种事件。最后一步 src 属性，并将 javascript 文件添加到 head。

动态脚本加载是非阻塞 Javascript 下载中最常用的模式，因为它可以跨浏览器，而且简单易用。

 

#### XMLHttpRequest Script Injection XHR脚本注入

另一个以非阻塞方式获得脚本的方法是使用 XMLHttpRequest(XHR) 对象将脚本注入到页面中。此技术首先创建一个 XHR 对象，然后下载 Javascript 文件，接着用一个动态`<script>`元素将 Javascript 代码注入页面。看 demo：　

```
var xhr = new XMLHttpRequest(); 
xhr.open("get", "file1.js", true); 
 xhr.onreadystatechange = function(){
    if (xhr.readyState == 4){
     if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304){ // 检查http状态码
        var script = document.createElement("script"); 
        script.type = "text/javascript";
        script.text = xhr.responseText;
        document.body.appendChild(script);
      } 
   }
}; 
xhr.send(null);
```

此代码向服务器发送一个获取 file1.js 的文件 get 请求。onreadystatechange 事件处理函数检查 readyState 是不是4，然后检查 http 状态码是不是有效（200表示确定客户端请求已成功，2xx表示有效回应，304表示一个缓存响应）。如果收到一个有效响应，那么就创建一个新的`<script>`元素，将它的文本属性设置为从服务器接收到的 responseText 字符串。这样做实际上会创建一个带有内联代码的`<script>`元素，一旦新的`<script>`元素被添加到文档，代码将被执行，并准备使用。

此方法的优点是兼容性佳，且你可以下载不立即执行的 Javascript 代码。由于代码返回在`<script>`标签之外，它下载后不会自动执行，这使得你可以推迟执行。

此方法的确定是受到浏览器同源限制，Javascript 文件必须与页面放置在同一个域内，不能从 CDN(内容分发网络 Content Delivery Network) 下载。正因为这个原因，大型网页通常不采用XHR脚本注入技术。

 

#### Recommended Noblocking Pattern 推荐的非阻塞模式

推荐的向页面加载大量 Javascript 的方法分为两个步骤：

第一步，包含动态加载 Javascript 所需的代码，然后加载页面初始化所需的除了 Javascript 之外的部分。这部分代码尽量小，可能只包含`loadScript()`函数，它的下载和运行非常迅速，不会对页面造成很大干扰。
第二步，当初始代码准备好之后，用它来加载其余的 Javascript。
例如：

```
<script type="text/javascript" src="loader.js">
</script> <script type="text/javascript">
loadScript("the-rest.js", function(){ 
　　Application.init();
}); 
</script>
```

将此代码放置在 body 的关闭标签`</body>`之前。这样做的好处是，首先，这样确保 Javascript 运行不会影响其他页面的其他部分显示。其次，当第二部分 Javascript 文件完成下载，所有应用程序所必须的 DOM 已经创建完毕，并做好被访问的准备，避免使用额外的事件处理（如`window.onload`）来得知页面是否已经准备好了。

另一个选择是直接将`loadScript()`函数嵌入在页面中，这可以减少一个 http 请求的开销。例如：

```
<script type="text/javascript"> 
  function loadScript(url, callback){
    var script = document.createElement ("script");
　  script.type = "text/javascript";
   
    if (script.readyState){ //IE script.onreadystatechange = function(){
      if (script.readyState == "loaded" || script.readyState == "complete"){
        script.onreadystatechange = null; 
        callback();
      } 
    };
  } else { //Others 
   script.onload = function(){
   　　callback(); 
   };
  }
  script.src = url; 
  document.getElementsByTagName("head")[0].appendChild(script);
}

loadScript("the-rest.js", function(){
　　Application.init(); 
});
</script>
```

一旦页面初始化代码下载完成，还可以使用`loadScript()`函数加载页面所需的额外功能函数。

介绍一个通用的工具，Yahoo! Search 的 Ryan Grove 创建了 LazyLoad 库（参见：http://github.com/rgrove/lazyload/ ）。LazyLoad 是一个强大的`loadScript()`函数。LazyLoad 精缩之后只有大约1.5KB。用法举例如下：

```
<script type="text/javascript" src="lazyload-min.js"></script> 
<script type="text/javascript">
    LazyLoad.js("the-rest.js", function(){ 
    Application.init();
    }); 
</script>
```
　　

## 总结

- 将所有`<script>`标签放置在页面底部，紧靠关闭标签`</body>`的上方。此方法可以保证页面在脚本运行之前完成解析。
- 将脚本成组打包。页面的`<script>`标签越少，页面的加载速度就越快，响应也更迅速。不论外部脚本文件还是内联代码都是如此。

有几种方法可以使用非阻塞方式下载 Javascript：
- 为`<script>`标签添加 defer 属性
- 动态创建`<script>`元素，用它下载并执行代码
- 用 XHR 对象下载代码，并注入到页面

**通过上述策略，可以极大提高那些使用 Javascript 代码的网页应用的实际性能。**