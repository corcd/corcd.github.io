---
layout:       post
title:        "git 命令的简单用法"
subtitle:     " git 命令用法，快速入门 git 的简单应用"
date:         2017-10-23
author:       "corcd"
header-img:   "img/post-bg-git.jpg"
header-mask:  0.3
catalog:      true
tags:
    - 技术
    - git
---

# 前言

相信每一个第一次接触 git 的小伙伴来说，git 给的第一印象体验可能并不是太好，毕竟黑乎乎的命令行窗口、一条条抽象的 command 语句，怎么来说也没有桌面图形界面来的直观明了。不过呢，在深入接触 git 的用法之后，git 那简洁的格式而又强大的功能，会让大家真真切切地领略到 git 独具的特殊魅力，以及无与伦比的操作“爽感”，2333😂。

# 让我们开始吧

废话不多说啦，直接进入正题

## 1.1 Git 初始化配置 

- 配置使用 git 仓库的人员姓名 

    `$ git config --global user.name "Your Name Comes Here"`

- 配置使用 git 仓库的人员 email 

    `$ git config --global user.email you@yourdomain.example.com` 

## 1.2 Git 仓库初始化

- 在进入需要的目录之后，初始化本地 git 仓库

    `$ git init`

## 1.3 Git 分支操作

- 创建一个新分支，并切换到该分支上 

    `git checkout –b 新分支名`

- 切换到某个已经建立的本地分支 local_branch 

    `git checkout local_branch` 

- 切换到服务器上的某个分支 remote_branch 

    `git checkout remote_branch  #远程分支 remote_branch 可以通过 git branch –r 列出`

- 切换到某个 commit id 

    `git checkout commit_id` 


`$ git branch`

## 1.4 Git add 

- 可以当前工作目录中更改或者新增的文件加入到Git的索引中，加入到Git的索引中就表示记入了版本历史中，这也是提交之前所需要执行的一步

- 可以递归添加，即如果后面跟的是一个目录作为参数，则会递归添加整个目录中的所有子目录和文件，例如： 

    `$ git add dir1   #添加dir1这个目录，目录下的所有文件都被加入` 

    `$ git add f1 f2  #添加f1，f2文件` 

    `$ git add .      #添加当前目录下的所有文件和子目录`

## 1.5 Git rm

- 当然啦，有增（add）理所应当也会有对应的减（rm）操作，可以从当前的工作目录中和索引中删除文件

- 同样可以递归删除，即如果后面跟的是一个目录做为参数，则会递归删除整个目录中的所有子目录和文件，例如： 

    `$ git rm –r *      #进入某个目录中，执行此语句，会删除该目录下的所有文件和子目录` 

    `$ git rm f1        #删除文件f1，包含本地目录和index中的此文件记录`

- 综合用法

    `$git rm --ached f1 #删除文件f1`
    
    *(不会删除本地目录文件，只删除index中的文件记录；将已经git add的文件remove到cache中,这样commit的时候不会提交这个文件, 适用于一下子添加了很多文件, 却又想排除其中个别几个文件的情况)*

## 1.6 Git commit 

- 提交当前工作目录的修改内容，-m 参数可以指定当前操作的注释信息，信息不能为空

- git commit 还有一个 -a 的参数，可以将那些没有通过 git add 标识的变化一并强行提交，但是不建议使用这种方式

- 每一次提交，git就会为全局代码建立一个唯一的commit标识代码，用户可以通过git reset 命令恢复到任意一次提交时的代码

- 综合用法

    `$ git commit -a –m “message”  #在一个commit id上不断修改提交的内容`

## 1.7 Git 状态查看 

- 查看版本库的状态。可以得知哪些文件发生了变化，哪些文件还没有添加到git库中等等,建议每次commit前都要通过该命令确认库状态

    `$ git status`

- 查看历史日志，包含每次的版本变化。每次版本变化对应一个commit id

    `$ git log -1`

    *(-1的意思是只显示一个commit，如果想显示5个，就-5。不指定的话，git log会从该commit一直往后显示)* 

- 综合用法

    `$ git log --stat –summary  #显示每次版本的详细变`

## 1.8 comming song...




# 参考文档

- [GIT 的使用方法详解]  <http://blog.csdn.net/sunweizhong1024/article/details/8055400/>