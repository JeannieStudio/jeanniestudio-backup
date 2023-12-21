---
title: "手动搭建Trojan-go+Nginx+Tls"
date: 2020-07-18T00:29:12+08:00
description: 几分钟手动搭建Trojan-go，小白教程，炒鸡简单……
---

### 前言
**很多小伙伴在使用一键脚本搭建trojan-go实现科学上网的时候，偶尔会出现失败的情况，不妨试试手动搭建，花几分钟而已，100%成功，何乐而不为呢?**
<iframe width="560" height="315" src="https://www.youtube.com/embed/kJuhXhi-xkU" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### trojian和trojan-go的区别
trojan-go:使用Go实现的完整Trojan代理，与Trojan协议以及Trojan版本的配置文件格式兼容。安全，高效，轻巧，易用。
但是，trojan-go具有以下2个特性是trojan不具有的：
1.支持使用多路复用提升并发性能
2.支持CDN流量中转(基于WebSocket over TLS/SSL)。

* * *

### 准备条件：
1.一个vps，我使用谷歌云演示
2.一个域名（不会申请域名的小伙伴可以看这个视频：）,域名和vps的ip地址关联成功
### 申请tls证书

#### 一、简介
本文主要介绍使用acme.sh脚本来申请证书，并实现到期自动续订的步骤。acme.sh脚本实现了acme协议，可以从letsencrypt生成免费的证书。
#### 二、安装acme.sh脚本
##### 1.先切换到root
```
sudo -i
```

##### 2.安装依赖，debian或ubuntu执行：

```
apt-get update && apt-get -y install socat
```
centos执行：
```
yum update &&  yum -y install socat
```
##### 3.安装acme.sh脚本：
```
curl https://get.acme.sh | sh
```
**出现：Install success!表示安装成功**
##### 4.让环境变量生效，以后无论在哪个路径，直接使用acme.sh

```
source ~/.bashrc
```
#### 三、用acme.sh脚本申请证书
##### 1.开始签发证书,注意请将example.com替换成你的域名
```
acme.sh --issue -d example.com --standalone -k ec-256 --force
```
出现下图说明你的证书签发成功
![](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/trojan-go-001.jpg?raw=true)

##### 2.创建一个目录用于存放你的证书,注意请将example.com替换成你的域名
```
mkdir /data
```
```
mkdir /data/example.com
```
#### 四、复制/安装证书
注意请将example.com替换成你的域名 **(共3处)**
```
acme.sh --installcert -d example.com --fullchainpath /data/example.com/fullchain.crt --keypath /data/example.com/privkey.key --ecc --force
```
**至此，tls证书签发成功！！！**
#### 五、证书更新
关于从Letsencrypt申请到的证书，有效期为90天，脚本会每60天自动更新证书，你无须进行任何操作，今后可能会缩短这个时间，不过都是自动的，你不用关心。

### 下载伪装网站
#### 创建一个目录，用于放置网站
```
mkdir /usr/wwwroot
```
#### 下载并解压（如果下载失败了，再到这个网站[https://templated.co/](https://templated.co/)找一个模板下载）
```
wget -O /usr/wwwroot/web.zip --no-check-certificate https://templated.co/intensify/download
```
```
unzip -o -d /usr/wwwroot /usr/wwwroot/web.zip
```
### 编译安装nginx
#### 一、安装依赖包
##### 1.下载/安装 openssl-1.1.1（使 nginx 支持TLS 1.3）
```
cd /usr/local/src
```
```
wget -nc --no-check-certificate https://www.openssl.org/source/openssl-1.1.1g.tar.gz -P /usr/local/src
```
```
tar -zxvf  /usr/local/src/openssl-1.1.1g.tar.gz  -C /usr/local/src
```
##### 2.其他依赖
debian和ubuntu执行下面语句：
```
apt  -y install build-essential libpcre3 libpcre3-dev zlib1g-dev git  dbus manpages-dev aptitude g++
```
centos执行：
```
yum -y groupinstall "Development tools"
```
```
yum -y install pcre pcre-devel zlib-devel epel-release gcc gcc-c++
```
#### 二、下载解压nginx源码

```
wget -nc --no-check-certificate http://nginx.org/download/nginx-1.18.0.tar.gz -P /usr/local/src
```
```
tar -zxvf /usr/local/src/nginx-1.18.0.tar.gz -C /usr/local/src
```
#### 三、编译和安装nginx
##### 1.编译配置
```
cd /usr/local/src/nginx-1.18.0
```
```
mkdir /etc/nginx
```
```
./configure --prefix=/etc/nginx \
        --with-http_ssl_module \
        --with-http_gzip_static_module \
        --with-http_stub_status_module \
        --with-pcre \
        --with-http_realip_module \
        --with-http_flv_module \
        --with-http_mp4_module \
        --with-http_secure_link_module \
        --with-http_v2_module \
        --with-cc-opt='-O3' \
        --with-openssl=../openssl-1.1.1g
```
##### 2.编译&&安装
```
make && make install
```
##### 3、修改基本配置
```
sed -i 's/#user  nobody;/user  root;/' /etc/nginx/conf/nginx.conf
sed -i 's/worker_processes  1;/worker_processes  3;/' /etc/nginx/conf/nginx.conf
sed -i 's/    worker_connections  1024;/    worker_connections  4096;/' /etc/nginx/conf/nginx.conf
sed -i '$i include conf.d/*.conf;' /etc/nginx/conf/nginx.conf
```
##### 4、删除临时文件
```
rm -rf /usr/local/src/nginx-1.18.0
rm -rf /usr/local/src//nginx-1.18.0.tar.gz 
rm -rf /usr/local/src//openssl-1.1.1g
rm -rf /usr/local/src/openssl-1.1.1g.tar.gz
```
##### 5、添加配置文件
```
mkdir /etc/nginx/conf/conf.d
```

**注意：将下面代码中的端口号、域名和伪装网站目录修改成你自己的**

```
 cat >/etc/nginx/conf/conf.d/default.conf <<EOF
  server {
    listen 1-65535之间的端口号，避开特殊端口;
    server_name 你的域名;
    root /usr/wwwroot;
    ssl on;
    ssl_certificate   /data/example.com/fullchain.crt;
    ssl_certificate_key  /data/example.com/privkey.key;
	ssl_ciphers                 TLS13-AES-256-GCM-SHA384:TLS13-CHACHA20-POLY1305-SHA256:TLS13-AES-128-GCM-SHA256:TLS13-AES-128-CCM-8-SHA256:TLS13-AES-128-CCM-SHA256:EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+ECDSA+AES128:EECDH+aRSA+AES128:RSA+AES128:EECDH+ECDSA+AES256:EECDH+aRSA+AES256:RSA+AES256:EECDH+ECDSA+3DES:EECDH+aRSA+3DES:RSA+3DES:!MD5;
    ssl_prefer_server_ciphers    on;
    ssl_protocols                TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_session_cache            shared:SSL:50m;
    ssl_session_timeout          1d;
    ssl_session_tickets          on;
}
EOF
```
#### 四、配置nginx服务
##### 1.创建服务文件
```
cat >/etc/systemd/system/nginx.service <<EOF
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target
[Service]
Type=forking
PIDFile=/etc/nginx/logs/nginx.pid
ExecStartPre=/etc/nginx/sbin/nginx -t
ExecStart=/etc/nginx/sbin/nginx -c /etc/nginx/conf/nginx.conf
ExecReload=/etc/nginx/sbin/nginx -s reload
ExecStop=/bin/kill -s QUIT \$MAINPID
PrivateTmp=true
[Install]
WantedBy=multi-user.target
EOF
```
 
##### 2.加载并启动nginx
```
systemctl daemon-reload
```
```
systemctl restart nginx
```
##### 3.其他命令
```
systemctl start nginx  -启动
systemctl stop nginx  -停止
```

### 安装trojan-go
#### 一、下载trojan-go
##### 1.先切换到root
```
sudo -i
```
##### 2.新建一个目录，作为trojan的安装目录：
```
mkdir /etc/trojan
```
```
mkdir /etc/trojan/bin
```
##### 3.下载trojan-go最新版本
浏览器打开trojan-go的release页面：[https://github.com/p4gefau1t/trojan-go/releases](https://github.com/p4gefau1t/trojan-go/releases)，找到最新版本的release，目前是：v0.8.1。

这里有很多不同系统的release，找到自己的系统对应的版本。不知道自己的，可以执行下面这句查看系统版本：
```
uname -m
```
经过查询我的vps是x86_64，对应的版本是：trojan-go-windows-amd64.zip，执行下面语句下载：
```
wget --no-check-certificate -O /etc/trojan/bin/trojan-go-linux-amd64.zip "https://github.com/p4gefau1t/trojan-go/releases/download/v0.4.10/trojan-go-linux-amd64.zip"
```
如果执行上面那句报wget command not found ，debian和ubuntu请执行(没有报错就无需执行)：
```
apt -y install wget
```
centos请执行：
```
yum -y install wget
```
#### 二、解压/安装trojan-go
下载后解压：
```
unzip -o -d /etc/trojan/bin /etc/trojan/bin/trojan-go-linux-amd64.zip
```
如果执行上面那句报unzip command not found ，debian和ubuntu请执行(没有报错就无需执行)：
```
apt -y install unzip
```
centos请执行：
```
yum -y install unizip
```
**注意：**
如果您的是i386 或i686，请下载：trojan-go-linux-386.zip
```
wget --no-check-certificate -O /etc/trojan/bin/trojan-go-linux-amd64.zip "https://github.com/p4gefau1t/trojan-go/releases/download/v0.4.10/trojan-go-linux-386.zip"
```
解压执行：
```
unzip -o -d /etc/trojan/bin /etc/trojan/bin/trojan-go-linux-386.zip
```
如果是armv7l，请下载：trojan-go-linux-armv7.zip
```
wget --no-check-certificate -O /etc/trojan/bin/trojan-go-linux-amd64.zip "https://github.com/p4gefau1t/trojan-go/releases/download/v0.4.10/trojan-go-linux-armv7.zip"
```
解压执行：
```
unzip -o -d /etc/trojan/bin /etc/trojan/bin/trojan-go-linux-armv7.zip
```
**这样trojan-go就安装完成了**
### 配置trojan-go
**trojan-go安装完成后，开始配置**
#### 一、服务端配置
##### 1.先创建一个存放配置文件的目录：
```
mkdir /etc/trojan/conf
```
##### 2.创建服务端的配置文件：
修改以下6处，改好后执行下面所有的代码：
**注意，请修改以下6处后再执行：
1.将第5行local_port修改一个1-65535之间的端口，
2.第7行的端口号必须与nginx设置的端口一致
3.将第11行改为你的密码，
4.将第18、19、25、68、69行中的example.com改成自己的域名**

<details>  
<summary>点击显示所有代码</summary>


```
cat >/etc/trojan/conf/server.json <<EOF
{
  "run_type": "server",
  "local_addr": "0.0.0.0",
  "local_port": 1-65535之间的一个端口号，如13997,
  "remote_addr": "127.0.0.1",
  "remote_port": 与nginx端口保持一致,
  "log_level": 1,
  "log_file": "",
  "password": [
       "你的密码"
  ],
  "buffer_size": 32,
  "dns": [],
  "ssl": {
    "verify": true,
    "verify_hostname": true,
    "cert": "/data/example.com/fullchain.crt",
    "key": "/data/example.com/privkey.key",
    "key_password": "",
    "cipher": "",
    "cipher_tls13": "",
    "curves": "",
    "prefer_server_cipher": false,
    "sni": "example.com",
    "alpn": [
      "http/1.1"
    ],
    "session_ticket": true,
    "reuse_session": true,
    "plain_http_response": "",
    "fallback_port": 1234,
    "fingerprint": "firefox",
    "serve_plain_text": false
  },
  "tcp": {
    "no_delay": true,
    "keep_alive": true,
    "reuse_port": false,
    "prefer_ipv4": false,
    "fast_open": false,
    "fast_open_qlen": 20
  },
  "mux": {
    "enabled": false,
    "concurrency": 8,
    "idle_timeout": 60
  },
  "router": {
    "enabled": false,
    "bypass": [],
    "proxy": [],
    "block": [],
    "default_policy": "proxy",
    "domain_strategy": "as_is",
    "geoip": "./geoip.dat",
    "geosite": "./geoip.dat"
  },
  "websocket": {
    "enabled": false,
    "path": "",
    "hostname": "127.0.0.1",
    "obfuscation_password": "",
    "double_tls": false,
    "ssl": {
      "verify": true,
      "verify_hostname": true,
      "cert": "/data/example.com/fullchain.crt",
      "key": "/data/example.com/privkey.key",
      "key_password": "",
      "prefer_server_cipher": false,
      "sni": "",
      "session_ticket": true,
      "reuse_session": true,
      "plain_http_response": ""
    }
  },
  "forward_proxy": {
    "enabled": false,
    "proxy_addr": "",
    "proxy_port": 0,
    "username": "",
    "password": ""
  },
  "mysql": {
    "enabled": false,
    "server_addr": "localhost",
    "server_port": 3306,
    "database": "",
    "username": "",
    "password": "",
    "check_rate": 60
  },
  "redis": {
    "enabled": false,
    "server_addr": "localhost",
    "server_port": 6379,
    "password": ""
  },
  "api": {
    "enabled": false,
    "api_addr": "",
    "api_port": 0
  }
}
EOF

```
</details>

#### 二、启动trojan-go服务
##### 1.创建trojan-go服务文件
```
cat >/etc/systemd/system/trojan.service<< EOF
[Unit]
Description=trojan
Documentation=https://github.com/p4gefau1t/trojan-go
After=network.target

[Service]
Type=simple
StandardError=journal
PIDFile=/usr/src/trojan/trojan/trojan.pid
ExecStart=/etc/trojan/bin/trojan-go -config /etc/trojan/conf/server.json
ExecReload=
ExecStop=/etc/trojan/bin/trojan-go
LimitNOFILE=51200
Restart=on-failure
RestartSec=1s

[Install]
WantedBy=multi-user.target
EOF
```
##### 2.加载服务文件：
```
systemctl daemon-reload
```
##### 3.启动服务
```
systemctl start trojan.service
```
##### 4.其他的一些命令：
```
systemctl stop trojan.service   ------停止trojan-go
systemctl restart trojan.service  --------重启trojan-go
```

服务端和客户端配置文件都改好后，就可以进行科学上网了。但是，这个配置文件对于Trojan-go的新特效，一个都没有配置。
#### 三、新特性的配置
下面我们一个个来看看如何设置这些新特性：
##### 1.配置CDN流量中转
**服务器配置文件修改以下3点：**
1.第2行改为true
2.第3行改为一个url，必须以斜杠("/")开始，如：/my，客户端和服务端必须一致
3.第4行是域名

```
"websocket": {
    "enabled": true,
    "path": "/your-websocket-path",
    "host": "example.com", 
```

* host是主机名，一般填写域名。客户端host是可选的，填写你的域名。如果留空，将会使用remote_addr填充。

* path指的是websocket所在的URL路径，必须以斜杠("/")开始。路径并无特别要求，满足URL基本格式即可，但要保证客户端和服务端的path一致。path应当选择较长的字符串，以避免遭到GFW直接的主动探测。客户端的host将包含在Websocket的握手HTTP请求中，发送给CDN服务器，必须有效；服务端和客户端path必须一致，否则Websocket握手无法进行。
 
##### 2.使用多路复用提升并发性能
**服务端和客户端都只需要将false改为true即可**
```
"mux": {
    "enabled": true,
    "concurrency": 8,
    "idle_timeout": 60
  },
```


### 各平台客户端下载
**Windows/MacOS/Linux客户端Trojan-Qt5下载：**
[https://github.com/Trojan-Qt5/Trojan-Qt5/releases](https://github.com/Trojan-Qt5/Trojan-Qt5/releases)
**安卓客户端Igniter-Go下载：**

[https://play.google.com/store/apps/details?id=io.github.trojan_gfw.igniter&utm_source=github_readme&pcampaignid=pcampaignidMKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1&showAllReviews=true](https://play.google.com/store/apps/details?id=io.github.trojan_gfw.igniter&utm_source=github_readme&pcampaignid=pcampaignidMKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1&showAllReviews=true)


**ios客户端到AppStore下载shadowrocket**

*请注意哈，以上的Trojan-Qt5、Igniter-Go支持trojan-go的新特性，但shadowrocket不支持新特性。
如果你经常使用的是shadowrocket就不要启用新特性了。*

### 打赏
![](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E6%8D%90%E5%8A%A92.png?raw=true)














