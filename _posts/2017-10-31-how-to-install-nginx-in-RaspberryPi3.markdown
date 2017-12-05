---
layout:       post
title:        "在树莓派3上搭建 LNMP 服务器"
subtitle:     "新入树莓派的坑，一步一个脚印自己趟过去"
date:         2017-10-31
author:       "corcd"
header-img:   "img/post-bg-Raspberry.jpg"
header-mask:  0.3
catalog:      true
tags:
    - Raspberry Pi
    - Linux
---

# 故事还得从开始讲起

千呼万唤的开放实验室经费终于落实了下来，实验室里也是如愿以偿地购入了时下流行的开发板---树莓派3b UK 版。今天作为“硬盘”的 tf 卡万分期盼之中到了，不用多说，迫不及待地开始了探索树莓派的第一次旅程。

# 对树莓派进行初始配置

树莓派3b 较之前作树莓派2代，原生板载 WiFi 和蓝牙，可以说对于第一次配置来说，不需要硬连接 PC，直接可以通过 WiFi 局域网远程 SSH 连接配置，省去了 PC 端口配置的步骤，省时省力方便了很多。

当然，树莓派在一开始时是没有匹配连接本地的 WiFi 的，还是需要外部配置。烧录系统的步骤就不累赘了（其实 tf 卡买来带了系统 2333），不过方法不同版本都大同小异，树莓派官网上有详细的教程。在烧录好系统之后，将 tf 卡插入电脑读卡器，就可以开始 WiFi 的预配置。

### WiFi 网络配置

在 tf 卡根目录下打开 `/boot/wpa_supplicant.conf`，这个文件记录了 WiFi 的 SSID 、密码及各种信息，树莓派会在启动后会自行读取 `wpa_supplicant.conf` 配置文件连接 WiFi 设备。

操作方法：将刷好 Raspbian 系统的 tf 卡用电脑读取。在 boot 分区，也就是树莓派的 `/boot` 目录下新建 `wpa_supplicant.conf` 文件，按照下面的参考格式填入内容并保存。

```xml
country = CN
ctrl_interface=DIR = /var/run/wpa_supplicant GROUP = netdev
update_config = 1
 
network = {
    ssid = "WiFi-A"
    psk = "12345678"
    key_mgmt = WPA-PSK
    priority = 1
}
 
network = {
    ssid = "WiFi-B"
    psk = "12345678"
    key_mgmt = WPA-PSK
    priority = 2
    scan_ssid = 1
}
```
 > 说明以及不同安全性的 WiFi 配置示例：   
 > - ssid:无线网络的 ssid
 > - psk:无线网络密码
 > - priority:网络连接优先级，数字越大优先级越高（不能为负）
 > - scan_ssid:连接隐藏的 WiFi 时需要指定该值为1

如果你的 WiFi 没有密码：

```xml
network={
    ssid="你的无线网络名称（ssid）"
    key_mgmt=NONE
}
```

如果你的 WiFi 使用WEP加密：

```xml
network={
    ssid="你的无线网络名称（ssid）"
    key_mgmt=NONE
    wep_key0="你的wifi密码"
}
```

如果你的 WiFi 使用WPA/WPA2加密：

```xml
network={
    ssid="你的无线网络名称（ssid）"
    key_mgmt=WPA-PSK
    psk="你的wifi密码"
}
```

### 开启 SSH 服务

树莓派在之前的系统更新中默认关闭了系统的 SSH 服务，需要我们手动开启。同样在 boot 分区新建一个文件（空白的即可）文件命名为 ssh，注意要小写且不要有任何扩展名。
树莓派在启动之后会在检测到这个文件之后自动启用 SSH 服务。随后通过登录路由器管理页面，在 DHCP 客户端列表找到树莓派的 IP 地址，然后就可以通过 WiFi 在同一个无线局域网内使用 SSH 连接到树莓派了。

Windows 下需要安装额外的服务，macOS 系统自带了 SSH 服务，使用方法：

```shell
$ ssh (登录账户名)@(树莓派 IP 地址)
```

登录账户名默认为 `pi`，然后按提示输入登录密码（初始密码默认 `raspberry`）即可.

# 远程图形处理树莓派

前面通过 SSH 连接了局域网里远程的树莓派，但是只能进入到树莓派系统的命令行界面，想要方便快捷地浏览查看的话，还需要一个友好一点的图形界面。

### 安装 xrdp

可以在远程 PC & MAC 上安装 xrdp，然后使用远程桌面服务登录树莓派

```shell
$ sudo apt-get install xrdp
```
### 安装 tightvncserver

当然，更加方便的是通过 VNC 服务来对树莓派进行远程图形操作

命令行输入：

```shell
$ sudo apt-get install tightvncserver
```

安装好之后请一定先使用此命令设置一个VNC密码：

```shell
$ vncpasswd
```

手动启动 VNC 服务：

```shell
$ sudo tightvncserver -geometry 800x600 :1
```

> 先输入操作密码两次，然后会询问是否设置一个查看(view-only)密码，按自己喜欢，一般没必要。

*现在还有一个遗留下来的问题，远程图形界面无法获取到 root 权限，导致目前只能通过命令行模式更改根目录下的文件，还有待进一步探究*

# 安装互联网服务器组合配置

### 安装 Nginx 服务

Nginx 是一款时下流行的轻量级高性能服务器，全平台通用，同样也可以在树莓派系统下安装。

> 最新的系统已经内置 Apache2 服务器，不再需要我们手动安装

安装过程如下：

```shell
$ sudo apt-get update
$ sudo apt-get install nginx php5-fpm php5-cli php5-curl php5-gd php5-mcrypt php5-cgi
$ sudo apt-get mysql-client mysql-server
```

启动 Nginx 服务：

```shell
$ sudo service nginx start
$ sudo service php5-fpm restart
```

**这时候，由于某些原因，树莓派官方软件源访问速度极慢，会导致 mysql 的安装出现依赖关系错误，需要通过以下方法解决：**

#### 将软件源改为国内镜像

##### 将 pi 账户升级为 root 管理员账户

给 root 账户设置密码，并解锁 root 账户

```shell
$ sudo passwd root 
$ sudo passwd --unlock root 
```

切换到 root 账户
```shell
$ su root
```

##### 编辑/etc/apt/sources.list

使用管理员权限（经由 sudo），编辑`/etc/apt/sources.list`文件。参考命令行为：

```shell
$ sudo nano /etc/apt/sources.list
```

用#注释掉原文件内容，用以下内容取代，将软件源改为国内的清华大学镜像：

```txt
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
```

> 国内优秀源推荐：
> - 阿里云：//mirrors.aliyun.com/raspbian/raspbian/
> - 中科大：//lug.ustc.edu.cn/raspbian/raspbian/

*注意： 网址末尾的raspbian重复两次是必须的。因为Raspbian的仓库中除了APT软件源还包含其他代码。APT软件源不在仓库的根目录，而在raspbian/子目录下。 
编辑镜像站后，请使用`sudo apt-get update`命令，更新软件源列表，同时检查您的编辑是否正确。*

##### 编辑/etc/apt/sources.list.d/raspi.list

同样，使用管理员权限（经由 sudo），编辑`/etc/apt/sources.list.d/raspi.list`文件。参考命令行为：

```shell
$ sudo nano /etc/apt/sources.list.d/raspi.list
```

> 也可以使用 vi 命令在 Vim 中打开

用#注释掉原文件内容，用以下内容取代，将软件源改为国内的清华大学镜像：

```txt
deb http://mirror.tuna.tsinghua.edu.cn/raspberrypi/ stretch main ui
deb-src http://mirror.tuna.tsinghua.edu.cn/raspberrypi/ stretch main ui
```

> 上文中的 deb 地址都要注意树莓派系统的版本问题，最新的系统名称为 stretch，不要搞混，否则很可能安装不上

##### 更新软件源列表及已安装的包

```shell
$ sudo apt-get update & upgrade -y
```

更新完毕后，重复之前安装 nginx 的步骤，然后就可以成功安装 mysql 服务了。

# 至此服务器搭建完成

如果有需要的话，还可以手动安装 phpmyadmin 服务，方法如下：
```shell
$ sudo apt-get install phpmyadmin
```

平时使用时，记得先开启 nginx 和 mysql 服务，这样才能正常的显示网页，网页默认 URL 为`http://(树莓派 IP 地址)/...`
```shell
$ sudo service mysql start
$ sudo service nginx start
```

> 如使用 Apache2 则后者为`$ sudo service apache start`

# 分享一点不错的东西

### 推荐的应用

![](/img/vnc.png) 

[VNC Viewer，强大且实用的远程桌面工具，支持全平台 ](https://www.realvnc.com/en/connect/download/viewer/)

-------

# 参考文档


1. [无屏幕和键盘配置树莓派 WiFi 和 SSH  --树莓派实验室](http://shumeipai.nxez.com/2017/09/13/raspberry-pi-network-configuration-before-boot.html)

2. [为树莓派更换国内镜像源  --CSDN](http://blog.csdn.net/la9998372/article/details/77886806)