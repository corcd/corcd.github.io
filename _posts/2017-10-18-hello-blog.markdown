---
layout: post
title: "Hello Blog"
subtitle: "Hello World, Hello Blog"
date: 2017-10-17
author: "corcd"
header-img: "img/post-bg-2015.jpg"
tags:
    - 生活
    - Blog
---

> “Yeah It's On. ”


## 前言

简单地说，Corcd 的 Blog 就这么开通了。

[跳过废话，直接看技术实现 ](#build) 

第一次接触 ruby && jekyll ,在大佬的教程之下自己搭了一个基于 Markdown 和 Github 的 page 服务的个人博客，中间也遇到了不少坑，blog 的框架反复搭了好几次，遇到了一些莫名其妙的问题，所幸的是，最终自己努力解决了混淆的地方，成功的打开了自己的第一个个人博客。鼓掌鼓掌！！！ 


<p id = "build"></p>
---

## 正文

接下来说说搭建这个博客的技术细节。  

正好之前就有关注过 [GitHub Pages](https://pages.github.com/) + [Jekyll](http://jekyllrb.com/) 快速 Building Blog 的技术方案，非常轻松时尚。

其优点非常明显：

* **Markdown** 带来的优雅写作体验
* 非常熟悉的 Git workflow ，**Git Commit 即 Blog Post**
* 利用 GitHub Pages 的域名和免费无限空间，不用自己折腾主机
	* 如果需要自定义域名，也只需要简单改改 DNS 加个 CNAME 就好了 
* Jekyll 的自定制非常容易，基本就是个模版引擎


配置的过程中也没遇到什么坑，基本就是 Git 的流程，相当顺手

大的 Jekyll 主题上直接 fork 了 Clean Blog（这个主题也相当有名，就不多赘述了。唯一的缺点大概就是没有标签支持，于是我给它补上了。）

本地调试环境需要 `gem install jekyll`，结果 rubygem 的源居然被墙了……后来手动改成了我大淘宝的镜像源才成功

Theme 的 CSS 是基于 Bootstrap 定制的，看得不爽的地方直接在 Less 里改就好了（平时更习惯 SCSS 些），**不过其实我一直觉得 Bootstrap 在移动端的体验做得相当一般，比我在淘宝参与的团队 CSS 框架差多了……**所以为了体验，也补了不少 CSS 进去

最后就进入了耗时反而最长的**做图、写字**阶段，也算是进入了**写博客**的正轨，因为是类似 Hack Day 的方式去搭这个站的，所以折腾折腾着大半夜就过去了。

第二天考虑中文字体的渲染，fork 了 [Type is Beautiful](http://www.typeisbeautiful.com/) 的 `font` CSS，调整了字号，适配了 Win 的渣渲染，中英文混排效果好多了。


## 后记

现在 blog 还是只有一个初步的框架，UI 也是最基本的，我给自己订了一个计划，在接下来的日子里自己要逐渐的完善 blog 的基本功能，引入一些好玩的东西，然后写一个自定义的漂亮的 UI 模板。总而言之，我会仔细认真的维护着这个小小的 blog ，加油吧！

—— corcd 后记于 2017.10


