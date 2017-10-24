---
layout:       post
title:        "Git 的简单用法"
subtitle:     "快速入门 Git 简单应用"
date:         2017-10-23
author:       "corcd"
header-img:   "img/post-bg-git.jpg"
header-mask:  0.3
catalog:      true
tags:
    - 技术
    - git
---

> Git 真是个好东西

# 写在前面

相信每一个第一次接触 git 的小伙伴来说，git 给的第一印象体验可能并不是太好，毕竟黑乎乎的命令行窗口、一条条抽象的 bash 语句，怎么来说也没有桌面图形界面来的直观明了。不过呢，在深入接触 git 的用法之后，git 那简洁的格式而又强大的功能，会让大家真真切切地领略到 git 独具的特殊魅力，以及无与伦比的操作“爽感”，2333😂

-------

# 让我们开始吧

废话不多说啦，直接进入正题

### 1.1 Git 初始化配置 

- **配置使用 git 仓库的人员姓名**

    ```shell
    $ git config --global user.name "Your Name Comes Here"
    ```

- **配置使用 git 仓库的人员 email** 

    ```shell
    $ git config --global user.email you@yourdomain.example.com
    ```

### 1.2 Git 本地仓库初始化

- **在进入需要的目录之后，初始化本地 git 仓库**

    ```shell
    $ git init
    ```

### 1.3 Git 分支操作

#### Git checkout

- **创建一个新分支，并切换到该分支上**

    ```shell
    $ git checkout –b [new_branch_name]
    ```

- **切换到已经建立的本地分支 local_branch 上** 

    ```shell
    $ git checkout [local_branch]
    ``` 

- **切换到服务器上的分支 remote_branch** 

    ```shell
    $ git checkout [remote_branch]
    ```
    
    > 提示：远程分支 [remote_branch] 可以通过 `$ git branch –r` 列出

- **切换到标识 id 为 commit id 的分支上** 

    ```shell
    $ git checkout [commit_id]
    ``` 

#### Git branch

- **创建一个分支**

    ```shell
    $ git branch [branch_name]
    ```

    *(虽然创建了分支，但是不会将当前工作分支切换到新创建的分支上，因此，还需要命令`$ git checkout [branch_name]` 来切换)*

    > 区别：如果使用`$ git checout –b [branch_name]` ，不仅创建了分支，还将当前工作分支切换到了该分支上

- **删除分支** 

    ```shell
    $ git branch –D [branch_name]
    ``` 

    > 注意： 强制删除了此分支，而且删除后，发生在该分支的所有变化都无法恢复

### 1.4 Git add 

- **可以将当前工作目录中更改或者新增的文件加入到 git 的索引中，即为记入了版本历史中，这也是提交之前所必须执行的一步**

- **可以递归添加，即如果后面跟的是一个目录作为参数，则会递归添加整个目录中的所有子目录和文件，例如：** 

    ```shell
    $ git add [dir]            #添加[dir]这个目录，目录下的所有文件都被加入

    $ git add [file1] [file2]  #添加[file1]，[file2]文件

    $ git add .                #添加当前目录下的所有文件和子目录
    ```

### 1.5 Git rm

- **当然，有增（add）,理所应当也会有对应的减（rm）操作，可以从当前的工作目录中和索引中删除文件**

- **同样可以递归删除，即如果后面跟的是一个目录做为参数，则会递归删除整个目录中的所有子目录和文件，例如：** 

    ```shell
    $ git rm –r *            #进入某个目录中，执行此语句，会删除该目录下的所有文件和子目录

    $ git rm [file]          #删除文件[file]，包含本地目录和index中的此文件记录

    $ git rm --ached [file]  #删除文件[file]
    ```
    
    > 提示：加入 --ached 参数后，不会删除本地目录文件，只删除 index 中的文件记录；将已经 git add 的文件 remove 到 cache 中,这样 commit 的时候不会提交这个文件, 适用于一下子添加了很多文件, 却又想排除其中个别几个文件的情况

### 1.6 Git 代码操作 

#### Git merge

- **将服务器上下载的代码和本地代码合并，或者进行分支合并** 

    ```shell
    $ git merge [branch_1] [branch_2]  #合并两个分支
    ```

#### Git diff 

- **把本地的代码和 index 中的代码进行比较，或者把 index 中的代码和本地仓库中的代码进行比较**

    ```shell
    $ git diff           #比较工作目录和 Index 中的代码

    $ git diff --cached  #比较 index 和本地仓库中的代码
    ```

### 1.7 Git 状态查看 

- **查看版本库的状态**

    ```shell
    $ git status
    ```

    > 提示：可以得知哪些文件发生了变化，哪些文件还没有添加到 git 库中等等，建议每次 commit 前都要通过该命令确认库状态

- **查看历史日志，包含每次的版本变化，每次版本变化对应一个 commit id**

    ```shell
    $ git log -1

    $ git log --stat –summary  #显示每次版本的详细变化
    ```

    > 提示：-1的意思是只显示一个commit，如果想显示5个，就-5。不指定的话，git log会从该commit一直往后显示

### 1.8 Git commit 

- **提交当前工作目录的修改内容**

- **每一次提交，git 就会为全局代码建立一个唯一的 commit 标识代码，用户可以通过 git reset 命令恢复到任意一次提交时的代码**

    ```shell
    $ git commit -a –m “[message]”
    ```

    > 提示：，-m 参数可以指定当前操作的注释信息，信息不能为空；git commit 还有一个 -a 的参数，可以将那些没有通过 git add 标识的变化一并强行提交，但是不建议使用这种方式

### 1.9 Git 远程服务器操作

#### Git clone

- **克隆服务器的仓库的代码到本地建立的目录中** 

    ```shell
    $ git clone [url]
    ```

    *(通过 git clone 获取的远端 git 库，只包含了远端 git 库的当前工作分支。如果想获取其它分支信息，需要使用 `$ git branch –r` 来查看， 如果需要将远程的其它分支代码也获取过来，可以使用命令 `$ git checkout -b [local_branch] [remote_branch]`，其中，远端分支名  [remote_branch] 为 `$ git branch –r ` 所列出的分支名， 一般是诸如 `origin/branch_name` 的样子；如果本地分支名 [local_branch] 已经存在， 则不需要 `-b` 参数)*

    > 注意：通过 git clone 获取远端 git 库后，.git/config 中的开发者信息不会被一起 clone 过来。仍然需要为本地库的 .git/config 文件添加开发者信息。此外，开发者还需要自己添加 .gitignore 文件

#### Git fetch 

- **从服务器的仓库中下载最新代码** 

    ```shell
    $ git fetch [url]
    ```

    > 区别：相比 git pull 更安全些，相当于从远程获取最新版本到本地，不会自动 merge 

#### Git remote 

- **查看当前的远程仓库**

    ```shell
    $ git remote     #不带参数，列出已经存在的远程分支

    $ git remote -v  #出详细信息，在每一个名字后面列出其远程url
    ```

- **添加远程仓库**

    ```shell
    $ git remote add [shortname] [url]
    ```

    > 提示：要添加一个目标地址为 [url] 的远程仓库,可以指定一个简单的 [shortname] ,以便将来引用


#### Git pull 

- **从服务器的仓库中获取代码，和本地代码合并，等同于： git fetch + git merge** 

- **从其它的版本库（既可以是远程的也可以是本地的）将代码更新到本地，例如：`$ git pull origin master` 就是将 origin 这个版本库的代码更新到本地的 master 主分支** 

    ```shell
    $ git pull username@ipaddr:[remote_repository] [remote_branch] [local_branch]
    ```

    *(这条命令将从远端仓库 [remote_repository] 中的远端分支名 [remote_branch] 获取到本地 git 库的一个本地分支中。其中，如果不写本地分支名 [local_branch]，则默认 pull 到本地当前分支)* 

    > 注意：git pull 也可以用来合并分支。 和 git merge 的作用相同。 因此，如果你的本地分支已经有内容，则 git pull 会合并这些文件，如果有冲突会报警

#### Git push

- **将本地 commit 提交的代码更新到远程仓库的 [remote_branch] 分支中**

    ```shell
    $ git push origin [remote_branch]
    ```

    > 区别：git push 和 git pull 正好想反，是将本地某个分支的内容提交到远端某个分支上。用法：`$ git pushusername@ipaddr:[remote_repository] [local_branch] [remote_branch]`，这条命令将本地 git 库的一个本地分支 push 到远端 git 库的远端分支名 [remote_branch] 中

    > 注意：git push 好像不会自动合并文件。因此，如果 git push 时，发生了冲突，就会被后 push 的文件内容强行覆盖，而且没有什么提示。 这在合作开发时是很危险的事情

# 还有话说...

### 推荐的应用

**整理了这么多，最后嘛给大家推荐一个很棒很 Nice 的应用程序 :**

![](/img/Sourcetree-ico.svg) 

**[Sourcetree](https://www.sourcetreeapp.com/),这是一个图形化的 git 管理应用，支持 Win 和 macOS，直观明了的操作让 git 的使用体验指数上升，值得一试！**

-------

# 参考文档


1. [Git 使用详细介绍](http://blog.csdn.net/gemmem/article/details/7290125)

2. [git 命令之 git remote 的用法](http://blog.csdn.net/wangjia55/article/details/8802490)