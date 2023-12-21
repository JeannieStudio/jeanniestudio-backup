---
title: "一键搭建Trojan"
date: 2020-02-21T00:29:12+08:00
description: 一键搭建Trojan，小白教程，炒鸡简单……
---

## 准备工作：

**1.准备一台vps**（[教程链接](https://youtu.be/YvyMHNxapV0)）

**2.申请一个域名**（[教程链接](https://youtu.be/VZJF-POGKVU)）

**3.域名解析**（[教程链接](https://youtu.be/YvyMHNxapV0)） 


## 安装 Trojan

首先切换root用户，执行：

```
sudo -i
```


安装依赖包(Debian为例)，执行：

```
apt-get update && apt-get install sudo whiptail curl locales -y && sudo -i
```


执行一键安装脚本：（项目链接：[https://github.com/johnrosen1/trojan-gfw-script](https://github.com/johnrosen1/trojan-gfw-script)）

```
sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/johnrosen1/trojan-gfw-script/master/vps.sh)"
```

到这一步的时候我把7、8、9、10下载相关的项都去掉了。如果你有这方面的需求可以不用去。

![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/001.jpg?raw=true)

这一步输入密码的时候，第一次搭建输入了一个非常复制的密码，搭建没有成功，不知道是不是自己输入的密码不符合脚本的规则，建议自动生成就行了。第二个密码也如此。

![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/002.jpg?raw=true)

这一步选择“否”即可，如果选择“是”还要去网站查安全码，比较麻烦。

![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/003.jpg?raw=true)

**其他步骤都默认安装就行了。**

##  Trojan客户端

**windows客户端：**


[https://github.com/trojan-gfw/trojan/releases](https://github.com/trojan-gfw/trojan/releases)

**配置和使用：**
下载trojan-1.14.1-win.zip后解压，配置config.json

**只需修改2处：
1."remote_addr": 换成自己的域名；
2."password":换成自己第一次输入的密码即可（如果是自动生成，结果页面会显示该密码）**

修改完保存，运行trojan.exe即可，但这样还不能科学上网，还需要运行v2rayN才行。原因是trojan只支持socks，所以要把https转换为socks流量，而且v2rayN还可以分流。


下载v2rayN-core:
[https://github.com/2dust/v2rayN/releases/latest](https://github.com/2dust/v2rayN/releases/latest)

解压后运行v2rayN.exe，按下图填写：

![v2ray配置](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/v2%E9%85%8D%E7%BD%AE001.jpg?raw=true)

 其中端口可以改，但必须和trojan客户端的配置文件中的"local_port"保持一致。
 
 别忘了打开http代理，大功告成！！！
 
**苹果端：**

下载shadowroket，苹果商店售价：$2.99，作者再某宝上买的小火箭兑换码，花了3.99人民币。

配置非常简单，添加服务，扫描trojan搭建完成后生成的二维码即可。

![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/ios01.jpg?raw=true)

**安卓端：**

从下面链接下载app-release.apk，完成后安装，安装完成后只需填入域名和密码1就可以了。https://github.com/trojan-gfw/igniter/releases

![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E5%AE%89%E5%8D%9301----.jpg?raw=true)



## 捐助本站

![二维码](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E6%8D%90%E5%8A%A92.png?raw=true)