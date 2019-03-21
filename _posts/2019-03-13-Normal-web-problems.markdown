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

### 不可见缓存图片
首先利用Image()构造函数来创建一个屏幕外图片对象，之后将该对象的src属性设置期望的URL，由于图片元素并没有添加到文档中，因此它是不可见的，但是浏览器还是会加载图片并将其缓存起来
```
function preloader(){
    var imageObj = new Image();
    var images = ["image1.jpg", "image2.jpg", "image3.jpg"];  //images数组存放的是要缓存的图片的URL路径
    for(i=0; i < images.length; i++ ){
        imageObj.src=images[i]  //加载图片到本地客户端
    }
}
```

### 为什么 jQuery 中使用 on（）绑定多个函数到同一事件是不会被覆盖
用 addEventListener 同样可以同一种类型的事件注册多个事件句柄（有别于 onXXX 注册方式），但是 jQuery 内部并没有采用这个方法，为了兼容所有的浏览器和实现事件的委托，设计的极为复杂，使用数组的方式来存放事件处理函数，触发事件时遍历这个数组执行

### Top K 问题

### Two Sum 问题

```
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

### 




