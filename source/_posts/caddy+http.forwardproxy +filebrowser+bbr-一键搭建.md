---
title: caddy+http.forwardproxy +filebrowser+bbr-一键搭建
date: 2020-03-14 15:41:11
tags:
description: 一键搭建caddy+http.forwardproxy +filebrowser，安全又稳定
---
### 前言
**大家是否知道caddy有一款插件叫做：http.forwardproxy，只要我们安装上这二者，然后稍加配置，就可以愉快的在世界网络中穿梭了，是不是很神奇?Let's get started.**
### 安装并配置
#### 安装caddy+http.forwardproxy 
下面直接给出了一键搭建脚本，想了解原理和手工搭建过程的可以参考这个：[链接](https://jeanniestudio.top/2020/03/14/caddy+http.forwardproxy%20+filebrowser--%E6%89%8B%E5%B7%A5%E6%90%AD%E5%BB%BA%E8%BF%87%E7%A8%8B/)

```
wget --no-check-certificate -O caddy-forwardproxy.sh https://raw.githubusercontent.com/JeannieStudio/jeannie/master/caddy-forwardproxy.sh
chmod +x caddy-forwardproxy.sh
./caddy-forwardproxy.sh 2>&1 | tee caddy-forwardproxy.log
```
安装bbr四合一脚本
```
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```

### 客户端配置
**windows端：**
我用的chrome浏览器，下载Proxy SwitchyOmega插件，安装如下填写：

![配置](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E9%85%8D%E7%BD%AE0000000.jpg?raw=true)
点击左下侧的“应用选项”就可以出国旅游了。

**ios端**
用小火箭即可，地址填域名，用户名和密码是Caddyfile里填的用户名和密码：
![ios](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/ios0001.jpg?raw=true)

* * *
**至此，您可以再世界网络穿梭了。**
* * *


### 配置伪装网站

**选泽一：伪装成一个非常卡哇伊的好玩的网站，类似下面这样**

![动图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/002.gif?raw=true)

依次执行下面这2条命令即可


```
apt-get install git -y
```

```
git clone https://github.com/HFIProgramming/mikutap.git /var/www
```

如果您选择了这个后不满意，又想安装下面那个私有云盘需要先执行下面两条命令再去执行私有云盘的脚本：
```
rm -rf /var/www
```
```
mkdir /var/www
```
**选择二：安装filebrowser伪装成一个个人在线私有云盘**
![](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/03.gif?raw=true)
上期给小伙伴演示了配置一个静态网站进行伪装，有朋友说比较浪费，好吧，那么就配置一个个人私有网盘，当个U盘使用吧，放上去的东西千万要本地备份，不然ip被封了文件找不回来了，哭都找不着地方哦😂

下面直接给出一键脚本，想了解如何手工安装可以参考这里：[链接](https://jeanniestudio.top/2020/03/14/caddy+http.forwardproxy%20+filebrowser--%E6%89%8B%E5%B7%A5%E6%90%AD%E5%BB%BA%E8%BF%87%E7%A8%8B/)
```
wget --no-check-certificate -O filebrowser.sh https://raw.githubusercontent.com/JeannieStudio/jeannie/master/filebrowser.sh
chmod +x filebrowser.sh
./filebrowser.sh 2>&1 | tee filebrowser.log
```
启动成功后在浏览器输入您的域名就可以看到上面那个个人网盘登录界面了。大功告成了！