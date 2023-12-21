---
title: "GCP谷歌云防火墙规则相关问题"
date: 2020-4-2T14:57:37+08:00
description: 想写个自动脚本在GCP的vps自动打开某端口的出入站，未实现
---
### 前言
*在GCP谷歌云vps上安装了ufw并配置后，没用、没用、没用，可悲的事情说三遍。我先哭一会儿……*
**事由：**
作者写了个一键脚本，想在谷歌云上自动开启某个端口，比如放行12345端口的入站和出站流量。
于是开始安装ufw防火墙管理工具
**ubuntu和debian**
```
apt install ufw -y
```
**centos**
```
yum install ufw -y
```
**安装完成后先查看防火墙状态，默认关闭或者未启用状态**
```
ufw status verbose
```
列出当前安装的程序
```
ufw app list
```
启用防火墙
```
ufw enable
```
禁用
```
ufw disable
```
其他开启相关进程防火墙
```
ufw allow ssh 
ufw allow http 
ufw allow https
```
### 谷歌云防火墙的创建
谷歌云防火墙的创建，要么从控制台手动创建，要么使用gcp提供的命令，如：
创建一条名为my的规则启用tcp，开启端口：12273-12274
```
gcloud compute firewall-rules create my \ 
--allow tcp:12273-12274
```
更多相关参考：
[https://cloud.google.com/sdk/gcloud/reference/compute/firewall-rules/create](https://cloud.google.com/sdk/gcloud/reference/compute/firewall-rules/create)
当可能会报以下错误：
```
- Insufficient Permission: Request had insufficient 
authentication scopes.
```

解决办法：
执行如下命令，并按提示进行确认并输入授权码，最终可以执行如上命令：
```
gcloud auth login
```
### 总结
可能是GCP处于安全考虑，总之，想用ufw操作防火墙在GCP这个平台没能实现，所以作者想写个一键脚本自动开启端口的夙愿未能实现，哎……如果有牛逼的大佬，有方法可以留言。

