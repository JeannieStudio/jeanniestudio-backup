---
title: IntelliJ IDEA 安装golang 插件
date: 2019-12-27 17:23:44
description: go语言开发会用到
---

## go语言环境安装

*详情可参考：*(https://www.runoob.com/go/go-environment.html)

##  IDEA 的官网下载

*社区版是免费的*：(https://www.jetbrains.com/idea/download/)

![下载地址](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/2.png?raw=true)


## golang插件安装
IDEA的golang插件是开源的，项目托管在github上面，下载最新版本并记录下COMPATIBLE BUILDS（*支持的版本号如：193.5662.53.103 *）

![下载地址](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/3.png?raw=true)


 打开 IDEA 工具后，选择 "Configure" -> "Plugins" 
 
 ![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/go-1.png?raw=true)
 
选择 "Install plugin from disk .."，再选择刚才下载的IDEA go 插件压缩包（注意：压缩包无需解压;已进入 Intellij Idea，可通过 File -> setting -> plugins 安装插件）。

![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/5.png?raw=true)

另外还可以直接通过IDEA进行下载即可
![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/4.png?raw=true)

## 关联 GO SDK

重启IDEA，"Configure"-->"settings"-->"Go"-->"GoRoot"，选中本地安装go的目录
![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/9.png?raw=true)

![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/7.png?raw=true)

 配置 GOPATH，（*也可在 windows 环境变量设置 GOPATH*）
 ![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/8.png?raw=true)
 
 至此，Intellij Idea + GO SDK + GO Plugin 全部安装完毕！
 
 ## 捐助本站

![二维码](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E6%8D%90%E5%8A%A92.png?raw=true)
