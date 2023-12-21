---
title: 无敌了！ssr+tls+caddy+bbr一键搭建
date: 2020-03-05 18:42:30
tags:
description: 速度与安全并存，一键搭建超级简单
---
### 前言
**由于很多萌新对配置caddy不是特别熟悉，所以整理了一个ssr+tls+acddy+bbr+网站网站的一键搭建的脚本，供大家参考。**


### 支持平台：
**Debian  9、10---测完
Ubuntu 18.04、19.10
Centos  7**

### 准备
**1.一个域名，推荐namesio(便宜，续费费用不会增加)
2.购买vps，本例以谷歌云为例**
### 安装

切换到root用户：

```
sudo -i
```
再执行一键搭建脚本：

```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/JeannieStudio/jeannie/master/ssr-caddy-tls.sh)"
```
安装bbr（只是锦上添花，不安装也可以出国）
```
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh"
chmod +x tcp.sh
./tcp.sh
```
### 注意事项
别忘了创建防火墙规则，放行80、443这四个端口的出入站，不然，额……你不让人家过，人家怎么帮你转发流量，自然也上不了网喽！

**视频链接：**
**订阅本频道：https://bit.ly/2X042ea**