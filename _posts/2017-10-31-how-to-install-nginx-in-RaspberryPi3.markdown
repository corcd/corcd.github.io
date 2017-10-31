---
layout:       post
title:        "在树莓派3b上搭建 LNMP 服务器"
subtitle:     "新入树莓派的坑，懵懵懂懂一开始什么都不明白，那只能一步一个脚印自己趟过去"
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

**千呼万唤的开发实验室经费终于落实了下来，实验室里也是如愿以偿地购入了时下流行的开发板---树莓派3b UK 版。今天作为“硬盘”的 tf 卡万分期盼之中到了，哈哈，不用多说，开始探索树莓派的第一次旅程。**

# 新手上路还算顺利

新接触树莓派，什么都还不懂，不过作为一个新时代的大学生，这种小问题还是难不倒我的。咳咳。。接下来还是正经一点。

树莓派3b 较之树莓派2代，原生就板载 WiFi 和蓝牙，可以说对于第一次配置来说，不需要硬连接 PC，直接可以通过 WiFi 局域网远程 SSH 连接配置，省去了 PC 端口配置的步骤，省时省力方便了很多。

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
 > **说明以及不同安全性的 WiFi 配置示例：**    
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
树莓派在启动之后会在检测到这个文件之后自动启用 SSH 服务。随后通过登录路由器管理页面，在 DHCP 客户端列表找到树莓派的 IP 地址，然后就可以通过 WiFi 通过 SSH 连接到树莓派了。

# 接下来的路上遇到了一点小麻烦

### 安装 xrdp

如果不想安装 VNC，那么可以安装 xrdp，然后使用远程桌面登录树莓派

```shell
sudo apt-get install xrdp
```
### 安装 tightvncserver

命令行输入：
```shell
sudo apt-get install tightvncserver
```

安装好之后请一定先使用此命令设置一个VNC密码：
```shell
vncpasswd
```
（先输入操作密码两次，然后会询问是否设置一个查看(view-only)密码，按自己喜欢，一般没必要。）

# 更大的麻烦阻挡了我的前进道路

### 安装 Nginx 服务

### 编辑/etc/apt/sources.list

使用管理员权限（经由 sudo），编辑`/etc/apt/sources.list`文件。参考命令行为：

```shell
$ sudo nano /etc/apt/sources.list
```

用#注释掉原文件内容，用以下内容取代：

```txt
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
```

> 国内优秀源推荐：

注意： 网址末尾的raspbian重复两次是必须的。因为Raspbian的仓库中除了APT软件源还包含其他代码。APT软件源不在仓库的根目录，而在raspbian/子目录下。 
编辑镜像站后，请使用`sudo apt-get update`命令，更新软件源列表，同时检查您的编辑是否正确。

### 编辑/etc/apt/sources.list.d/raspi.list

使用管理员权限（经由 sudo），编辑`/etc/apt/sources.list.d/raspi.list`文件。参考命令行为：

```shell
$ sudo nano /etc/apt/sources.list.d/raspi.list
```

用#注释掉原文件内容，用以下内容取代：

```txt
deb http://mirror.tuna.tsinghua.edu.cn/raspberrypi/ stretch main ui
deb-src http://mirror.tuna.tsinghua.edu.cn/raspberrypi/ stretch main ui
```

# 长路漫漫，分享一点不错的东西

### 推荐的应用

![](/img/vnc.png) 

[VNC Viewer，强大且实用的远程桌面工具，支持全平台 ](https://www.realvnc.com/en/connect/download/viewer/)

-------

# 参考文档


1. [无屏幕和键盘配置树莓派 WiFi 和 SSH](http://shumeipai.nxez.com/2017/09/13/raspberry-pi-network-configuration-before-boot.html)

2. [为树莓派更换国内镜像源](http://blog.csdn.net/la9998372/article/details/77886806)