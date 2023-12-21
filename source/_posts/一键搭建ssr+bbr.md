---
title: "一键搭建ssr+bbr"
date: 2019-12-30T14:57:37+08:00
description: 最初的ssr+bbr，留个备份也好……
---
## 创建vm实例

### 1.导航栏-Compute Engine-VM实例，进入实例页面

![进入页面](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/%E8%BF%9B%E5%85%A5%E9%A1%B5%E9%9D%A2.png?raw=true)


### 2.点击“创建实例”按钮


![创建实例](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/%E5%88%9B%E5%BB%BA%E5%AE%9E%E4%BE%8B.png?raw=true)

### 3.修改实例配置参数
![需修改内容1](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/%E5%A1%AB%E5%86%99%E5%86%85%E5%AE%B91.png?raw=true)
![需修改内容2](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/%E5%86%85%E5%AE%B9%E5%A1%AB%E5%86%992.png?raw=true)
### 4.创建成功后测试速度
连接https://www.ipip.net/，选择ping工具测试网速情况，响应时间在100ms内可以接受
![测速](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/ping%E7%BB%93%E6%9E%9C.png?raw=true)

## 一键搭建谷歌云
### 1.用谷歌云自带的ssh工具连接vps

### 2.切换到root用户下
使用命令：`sudo -i`

### 3.用秋水大神的一键脚本进行ssr的安装
执行脚本：
```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

![参数1](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/%E5%8F%82%E6%95%B01.png?raw=true)
![参数2](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/%E5%8F%82%E6%95%B02.png?raw=true)
![结果](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/%E7%BB%93%E6%9E%9C.jpg?raw=true)

当出现上图后，表示安装完成。*注意：请记下配置信息，以便客户端配置时使用。*

## 一键安装bbr

执行超级一键加速脚本，bbr 原版/魔改/plus/锐速 四合一脚本：


```
wget "https://github.com/chiakge/Linux-NetSpeed/raw/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```

至于选择哪个bbr版本，看个人喜好，我一般选择bbr plus.
![结果](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/bbr.png?raw=true)
至此，ssr+bbr搭建完成。

## 安装客户端

下面演示在win10安装ssr客户端软件：

## 捐助本站

![二维码](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E6%8D%90%E5%8A%A92.png?raw=true)