---
title: ssr+tls+caddy仍然稳得一批
date: 2020-02-26 17:23:44
tags: [编程,科学上网]
categories: 技术 
description: 带您一步步搭建ssr+caddy+tls出国旅游，超级简单，小白教程……
---
## Caddy与Nginx对比：

caddy具有以下优点：
1.go语言写的可以跨平台，无需安装任何依赖。
2.安装和配置简单，不管你是新的web开发者，还是专业人士，都能够快速上手。
3.默认使用Let's Encrypt提供的免费证书，自动让网站升级到HTTPS。最重要的是可以自动更新证书，永远不担心证书过期哟。
4.更合个人小型项目。

Ngnix的优点：
性能更好，适合做大型项目服务器。

## caddy安装和配置：

### 下载：

caddy官方下载地址：[https://caddyserver.com/v1/download](https://caddyserver.com/v1/download)

可以看到caddy官方脚本是：

```
curl https://getcaddy.com | bash -s personal
```

![脚本](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/caddy1.jpg?raw=true)

### 安装：

执行脚本安装即可：

```
curl https://getcaddy.com | bash -s personal
```

安装完成后，运行以下命令查询caddy安装目录：

```
which caddy
```

安装目录为：**/usr/local/bin/caddy** 。

启动caddy执行：

```
caddy
```

停止caddy执行重启vps命令：
```
 reboot
```


### caddy配置

[winscp连接vps教程：](https://jeanniestudio.github.io/2020/02/14/%E4%BD%BF%E7%94%A8WINSCP%E8%BF%9E%E6%8E%A5VPS%E6%95%99%E7%A8%8B--%E4%B8%80%E9%94%AE%E8%84%9A%E6%9C%AC/)

用winscp连接vps后，在/etc目录下创建caddy目录，之后创建名为Caddyfile的文件，文件内容如下：

**注意：域名、端口和邮箱改成自己的。**


**Caddyfile文件填写如下内容：**

```
http://example.com:1234:80这里有空格 {

 redir https://example.com:1234{url}
}
https://example.com:1234 这里有空格{
gzip
tls example@example.com
root /var/www/
}


```

**Caddy自动申请SSL证书位置：/root/.caddy/acme/acme-v01.api.letsencrypt.org/sites/xxx.xxx(域名)/**



## SSR 安装

**执行下列四合一脚本，并选择： ShadowsocksR进行安装：**

```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

**注：
"method"为 "none",
"protocol"选择"auth_chain_a",   
"obfs"选择tls1.2_ticket_auth"**



**各版本的管理命令如下：**

启动、关闭、重启、状态
 
 Shadowsocks-Python：
 
```
/etc/init.d/shadowsocks-python start|stop|restart|status
```

 ShadowsocksR:
 
```
/etc/init.d/shadowsocks-r start|stop|restart|status
```

Shadowsocks-Go:

```
/etc/init.d/shadowsocks-go start|stop|restart|status
```

 Shadowsocks-libev:
 
```
/etc/init.d/shadowsocks-libev start|stop|restart|status
```

**各版本默认配置文件**

Shadowsocks-Python

/etc/shadowsocks-python/config.json

ShadowsocksR

/etc/shadowsocks-r/config.json

Shadowsocks-Go

/etc/shadowsocks-go/config.json

Shadowsocks-libev

/etc/shadowsocks-libev/config.json

## ssr配置:

**修改配置文件：/etc/shadowsocks-r/config.json，只需要修改2处：**

1."server_port": 端口改为443

2.将"redirect": ""改为：
"redirect": ["*:443#127.0.0.1:1234"]
 添加这一句，注意1234这个端口号要和Caddyfile里的端口保持一致。

**修改好后重启ssr: /etc/init.d/shadowsocks-r restart.**

大功告成！！！

## 捐助本站

![二维码](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E6%8D%90%E5%8A%A92.png?raw=true)



