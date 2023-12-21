---
title: Jeannie出品-目前抵抗GFW最佳利器Trojan-go + Tls 二合一一键脚本
date: 2020-05-21 16:42:30
tags:
description: 速度与安全并存，一键搭建超级简单
---
### 前言
**今天给大家分享目前抵抗GFW最佳利器Trojan-go + Tls 二合一一键脚本供大家参考**


### 支持平台：
**Debian  9、10
Ubuntu 16.04  、18.04、19.10
Centos  7、8
**

### 准备
**1.一个域名，推荐namesio(便宜，续费费用不会增加)
2.购买vps，以谷歌云为例**
### 安装
切换到root用户：

```
sudo -i
```
centos先执行：
```
yum -y install curl
```
debian、ubuntu先执行:
```
apt -y install curl
```
执行一键搭建脚本：

```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/JeannieStudio/all_install/master/trojan-go_install.sh)"
```
安装bbr（只是锦上添花，不安装也可以出国）
```
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh"
chmod +x tcp.sh
./tcp.sh
```



**视频链接：https://youtu.be/uhke9t1syns**
**订阅本频道：https://bit.ly/2X042ea**