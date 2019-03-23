---
layout:       post
title:        "WebSocket 学习记录"
subtitle:     "记录学习 WebSocket 中的途径与感想"
date:         2019-03-23
author:       "corcd"
header-img:   null
header-mask:  0.05
catalog:      true
tags:
    - 技术
    - 前端
    - JavaScript
---

### 关于 WebSocket
WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议

WebSocket 允许服务端主动向客户端推送数据；在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输

现在，很多网站为了实现推送技术，所用的技术都是 Ajax 轮询。轮询是在特定的的时间间隔（如每1秒），由浏览器对服务器发出HTTP请求，然后由服务器返回最新的数据给客户端的浏览器。
传统的模式的缺点：即浏览器需要不断的向服务器发出请求，然而HTTP请求可能包含较长的头部，其中真正有效的数据可能只是很小的一部分 --> 浪费很多的带宽等资源

HTML5 定义的 WebSocket 协议，能更好的**节省服务器资源和带宽**，并且能够更**实时地进行通讯**

!(AJAX轮询)[http://www.runoob.com/wp-content/uploads/2016/03/ws.png]
!(WebSocket)[http://www.runoob.com/wp-content/uploads/2016/03/ws.png]

```
let Socket = new WebSocket(url, [protocol]);
```
第一个参数 url, 指定连接的 URL。第二个参数 protocol 是可选的，指定了可接受的子协议

> Websocket 使用和 HTTP 相同的 TCP 端口。默认情况下使用 80 端口；运行在 TLS 之上时，默认使用 443 端口

WebSocket 则是一个典型的应用层协议。

> Socket 是传输控制层协议，WebSocket 是应用层协议

### 属性
##### Socket.readyState	
只读属性 readyState 表示连接状态，可以是以下值：

- 0 - 表示连接尚未建立
- 1 - 表示连接已建立，可以进行通信
- 2 - 表示连接正在进行关闭
- 3 - 表示连接已经关闭或者连接不能打开

##### Socket.bufferedAmount	
只读属性 bufferedAmount 已被 send() 放入正在队列中等待传输，但是还没有发出的 UTF-8 文本字节数

### 事件
- open	Socket.onopen	连接建立时触发
- message	Socket.onmessage	客户端接收服务端数据时触发
- error	Socket.onerror	通信发生错误时触发
- close	Socket.onclose	连接关闭时触发

### 方法
##### Socket.send()	
使用连接发送数据

##### Socket.close()	
关闭连接

### 实例
```
<script type="text/javascript">
         function WebSocketTest()
         {
            if ("WebSocket" in window)
            {
               // 浏览器支持 WebSocket
               
               // 打开一个 web socket
               let ws = new WebSocket("ws://localhost:9998/echo");
                
               ws.onopen = function()
               {
                  // Web Socket 已连接上，使用 send() 方法发送数据
                  ws.send("发送数据");
                  alert("数据发送中...");
               };
                
               ws.onmessage = function (evt) 
               { 
                  let received_msg = evt.data;
                  alert("数据已接收...");
               };
                
               ws.onclose = function()
               { 
                  // 关闭 websocket
                  alert("连接已关闭..."); 
               };
            }
            
            else
            {
               // 浏览器不支持 WebSocket
            }
         }
      </script>
```

### 学习感想