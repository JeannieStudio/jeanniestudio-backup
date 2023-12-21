---
title: Jeannie出品-手动生成tls证书
date: 2020-06-02 22:42:30
tags:
description: 速度与安全并存，一键搭建超级简单

---
### 前言
**如果在使用六合一脚本，或者trojan-go二合一脚本的时候出现下图所示情况，请按下面的步骤手动生成tls证书**

![失败](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%AF%81%E4%B9%A6%E7%AD%BE%E5%8F%91%E5%A4%B1%E8%B4%A5.jpg?raw=true)


### 安装acme.sh
#### 什么是acme.sh

**简单来说acme.sh 实现了 acme 协议, 可以从 let‘s encrypt 生成免费的证书。**
acme.sh 有以下特点：

1.一个纯粹用Shell（Unix shell）语言编写的ACME协议客户端。
2.完整的ACME协议实施。 
3.支持ACME v1和ACME v2 支持ACME v2通配符证书
4.简单，功能强大且易于使用。你只需要3分钟就可以学习它。
5.Let's Encrypt免费证书客户端最简单的shell脚本。
6.纯粹用Shell编写，不依赖于python或官方的Let's Encrypt客户端。
7.只需一个脚本即可自动颁发，续订和安装证书。不需要root/sudoer访问权限。
8.支持在Docker内使用，支持IPv6

#### 安装acme.sh
执行命令：
```
curl  https://get.acme.sh | sh
```

**安装过程中会自动为你创建 cronjob, 每天 0:00 点自动检测所有的证书, 如果快过期了, 需要更新, 则会自动更新证书。**


### 申请证书
acme.sh 实现了 acme 协议支持的所有验证协议. 一般有两种方式验证: http 和 dns 验证。
下面主要介绍DNS api方式，如果对http方式感兴趣可以参考官方说明：
https://github.com/acmesh-official/acme.sh
#### DNS api方式
参考官方说明：

https://github.com/acmesh-official/acme.sh/wiki/dnsapi

由于acme.sh对域名解析/提供商的支持十分广泛,所以请针对自己所在的域名提供商获取对应的API Token，[支持列表](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)

**本文以cloudflare为例,CloudFlare Domain API提供了两种自动颁发证书的方法。我们选用第一种：**
![](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/cloudflaare%E7%94%9F%E6%88%90%E8%AF%81%E4%B9%A6.png?raw=true)
**首先[登录cloudflare](https://dash.cloudflare.com/login),进入overview,拉到页面最底部,点击“Get your Api token”**
![](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/api%E6%88%AA%E5%9B%BE.png?raw=true)

**然后点击Global API Key对应的view，获取“获取API密钥”**

![](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/cloudflaare%E7%94%9F%E6%88%90%E8%AF%81%E4%B9%A6.png?raw=true)
**最后在vps上执行以下命令，注意API密钥和邮箱换成自己的**

```
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export CF_Email="xxxx@sss.com"
```
**颁发证书执行以下命令，注意域名换成自己的**

```
acme.sh --issue --dns dns_cf -d www.example.com 
```
**修改目录名称执行，注意域名换成自己的**

```
mv /root/.acme.sh/www.example.com /root/.acme.sh/www.example.com_ecc
```
**安装证书执行以下命令，域名换成自己的**
```
/root/.acme.sh/acme.sh --installcert -d www.example.com --fullchainpath /data/www.example.com/fullchain.crt --keypath /data/www.example.com/privkey.key --ecc --force
```
**之后安装6合1或者trojan go二合一就可以顺利完成了，enjoy!!**

**视频链接：https://youtu.be/2Xys_rd4edQ**
**订阅本频道：https://bit.ly/2X042ea**