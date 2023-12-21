---
title: caddy+http.forwardproxy +filebrowser--手工搭建过程
date: 2020-03-14 15:39:05
tags:
description: 最新科学上网方式caddy+http.forwardproxy +filebrowser，快来试试吧
---
### 前言
**大家是否知道caddy有一款插件叫做：http.forwardproxy，只要我们安装上这二者，然后稍加配置，就可以愉快的在世界网络中穿梭了，是不是很神奇?Let's get started.**
### 安装并配置
#### 安装caddy+http.forwardproxy 
官网上就有安装命令，链接：[https://caddyserver.com/v1/download](https://caddyserver.com/v1/download)
```
curl https://getcaddy.com | bash -s personal http.forwardproxy
```

首先在/etc目录下新建一个名为caddy的目录，用于存放caddy的配置文件
```
mkdir /etc/caddy
```
然后再新建一个用于存放网站的目录，**注意替换您的域名**
```
mkdir /var/www/您的域名
```
下面要创建一个名为Caddyfile的配置文件，直接执行如下命令，**注意替换域名、邮箱，用户名和密码**
```
echo "http://您的域名:80  {
      redir https://您的域名:443{url}
} 
https://您的域名:443  {  
        gzip  
	timeouts none
	tls 您的邮箱
   root /var/www/您的域名
   forwardproxy {
       basicauth 你的用户名 你的密码
    }

}" > /etc/caddy/Caddyfile
```
这时候直接运行下面命令就可以启动caddy服务了，中间会询问你是否下载tls证书，选择y，就自动配置好tls证书了，是不是很贴心？？ 
```
caddy -conf /etc/caddy/Caddyfile
```
然鹅，这样启动了caddy服务后，这个终端就不能再执行其他命令了（除非重新打开一个ssh终端），关闭和重启caddy服务都很麻烦，我们希望执行一条命令就可以后台重启服务了，再执行一条命令就关闭了，这就需要安装进程管理工具。
* * *
#### 安装和配置Supervisor
Supervisor 是基于 Python 的进程管理工具，可以帮助我们更简单的启动、重启和停止服务器上的后台进程，是 Linux 服务器管理的效率工具。

安装执行命令：

```
sudo apt-get install supervisor
```
下面将caddy配置成一个服务，执行命令：（这条命令不用改任何东西）


```
echo "[program:caddy]command = /usr/local/bin/caddy -log stdout -agree=true -conf=/etc/caddy/Caddyfiledirectory = /etc/caddyautorstart=trueenvironment=CADDYPATH=/etc/ssl/caddy" > /etc/supervisor/conf.d/caddy.conf
```


supervisorctrl是supervisor的客户端工具，本身安装完会不加载任何配置启动，所以要先杀死
```
supervisorctl shutdown
```
再加载配置重新启动，这时候应该已经启动了caddy了，为了保险再执行下后两条吧
```
supervisord -c /etc/supervisor/supervisord.conf
```
关闭caddy服务
```
supervisorctl stop caddy
```
启动caddy服务
```
supervisorctl restart caddy
```
这样以后管理caddy就执行以下命令就可以了：

启动：supervisorctl start caddy
停止：supervisorctl stop caddy
重启：supervisorctl restart caddy
查看状态：supervisorctl status
安装目录为：/usr/local/bin/caddy
配置文件位置：/etc/caddy/Caddyfile

#### 安装bbr四合一脚本：
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
**至此，您可以再世界网络穿梭了。
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

安装filebrowser
```
curl -fsSL https://filebrowser.xyz/get.sh | bash
```
创建配置数据库
```
filebrowser -d /etc/filebrowser.db config init
```

设置监听地址(0.0.0.0表示任意地址的主机都可以访问个人云盘，您也可以换成您自己的个人主机IP)
```
filebrowser -d /etc/filebrowser.db config set --address 0.0.0.0
```
设置监听端口，端口号随意填写（1-65535）
```
filebrowser -d /etc/filebrowser.db config set --port 9978
```
设置语言环境
```
filebrowser -d /etc/filebrowser.db config set --locale zh-cn
```
设置日志位置
```
filebrowser -d /etc/filebrowser.db config set --log /var/log/filebrowser.log
```
用户名密码换成您自己的
```
filebrowser -d /etc/filebrowser.db users add 用户名 密码 --perm.admin
```
上述配置完成后，启动执行：
```
filebrowser -d /etc/filebrowser.db
```

启动成功后在浏览器输入 IP:端口，示例：http://您的vps的IP地址.1:9978就可以看到上面那个个人网盘登录界面了


* * *

* * *

**将File Browser配置成一个服务让它后台运行**

下载 File Browser 的 service 文件：
```
curl https://cdn.mivm.cn/www.mivm.cn/archives/filebrowser/filebrowser.service -o /lib/systemd/system/filebrowser.service
```

加载配置文件
```
systemctl daemon-reload
```
设置开机自启动：
```
systemctl enable filebrowser.service
```

下面是一些命令：

运行：systemctl start filebrowser.service
停止：systemctl stop filebrowser.service
重启：systemctl restart filebrowser.service
查看运行状态：systemctl status filebrowser.service

**caddy++filebrowser搭配：**
修改caddy配置文件,其实就是加了一行：proxy / 127.0.0.1:9978，注意端口号9978就是上面那个端口。
```
echo "http://您的域名:80  {
      redir https://您的域名:443{url}
} 
https://您的域名:443  {  
        gzip  
	timeouts none
	tls 您的邮箱
   root /var/www/您的域名
   forwardproxy {
        hide_ip
        hide_via
       basicauth 你的用户名 你的密码
    }
   proxy / 127.0.0.1:9978
}" > /etc/caddy/Caddyfile
```
重启caddy
```
supervisorctl restart caddy
```
启动成功后在浏览器输入 IP:端口，示例：http://您的vps的IP地址.1:9978就可以看到上面那个个人网盘登录界面了。大功告成了！