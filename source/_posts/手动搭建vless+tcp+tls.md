---
title: "手动搭建vless+tcp+tls"
date: 2020-08-25T00:29:12+08:00
description: 几分钟手动搭建vless，小白教程，炒鸡简单……
---

### 前言
**v2ray的官网转移到了[https://www.v2fly.org/](https://www.v2fly.org/)，而且添加了新的传输协议：vless,VLESS 是一个无状态的轻量传输协议，它分为入站和出站两部分，可以作为 V2Ray 客户端和服务器之间的桥梁。**


### vmess和vless的区别
**vless与VMess 不同，VLESS 不依赖于系统时间，认证方式同样为 UUID，但不需要 alterId**。

**官方附加说明：尽管 Websocket+TLS+Web 可能称得上是现阶段最好的方案，但绝对不是推荐新手一上来就尝试的方案，更不是 V2Ray 唯一的用法。同时，你应当了解，每个地区的网络状况不同 (主要指对不同协议的 QoS 程度)，你可以将所有配置都尝试一遍来寻找最适合自己的，尽量少问、最好不问“为什么我的 V2Ray 这么慢？”这样的问题。**

<iframe width="560" height="315" src="https://www.youtube.com/embed/SYAuvtF9wMY" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### 准备条件：
1.一个vps，我使用谷歌云演示
2.一个域名（不会申请域名的小伙伴可以看这个视频：）,域名和vps的ip地址关联成功

### 申请证书

#### 一、安装acme.sh脚本
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
#### 二、用acme.sh脚本申请证书
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
#### 三、复制/安装证书
注意请将example.com替换成你的域名 **(共4处)**
```
acme.sh --installcert -d example.com --fullchainpath /data/example.com/fullchain.crt --keypath /data/example.com/example.com.key --ecc --force
```
**至此，tls证书签发成功！！！**
#### 四、证书更新
关于从Letsencrypt申请到的证书，有效期为90天，脚本会每60天自动更新证书，你无须进行任何操作，今后可能会缩短这个时间，不过都是自动的，你不用关心。


### 安装nginx
#### 1.安装依赖包
**下载/安装 openssl-1.1.1（使 nginx 支持TLS 1.3）**
```
cd /usr/local/src
```
```
wget -nc --no-check-certificate https://www.openssl.org/source/openssl-1.1.1g.tar.gz -P /usr/local/src
```
```
tar -zxvf  /usr/local/src/openssl-1.1.1g.tar.gz  -C /usr/local/src
```
 **其他依赖**
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
#### 2.下载解压nginx源码

```
wget -nc --no-check-certificate http://nginx.org/download/nginx-1.18.0.tar.gz -P /usr/local/src
```
```
tar -zxvf /usr/local/src/nginx-1.18.0.tar.gz -C /usr/local/src
```
#### 3.编译和安装nginx
**编译配置**
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
 **.编译&&安装**
```
make && make install
```
**修改基本配置**
```
sed -i 's/#user  nobody;/user  root;/' /etc/nginx/conf/nginx.conf
sed -i 's/worker_processes  1;/worker_processes  3;/' /etc/nginx/conf/nginx.conf
sed -i 's/    worker_connections  1024;/    worker_connections  4096;/' /etc/nginx/conf/nginx.conf
sed -i '$i include conf.d/*.conf;' /etc/nginx/conf/nginx.conf
sed -i '/listen       80;/a\       return 301 https://$http_host$request_uri;' /etc/nginx/conf/nginx.conf

```
 **删除临时文件**
```
rm -rf /usr/local/src/nginx-1.18.0
rm -rf /usr/local/src/nginx-1.18.0.tar.gz 
rm -rf /usr/local/src/openssl-1.1.1g
rm -rf /usr/local/src/openssl-1.1.1g.tar.gz
```
**添加配置文件**
```
mkdir /etc/nginx/conf/conf.d
```

**注意：将下面代码中的端口号、域名和伪装网站目录修改成你自己的**

```
 cat >/etc/nginx/conf/conf.d/default.conf <<EOF
  server {
    listen 自定义一个1-65535的端口，不能是443;
    server_name 你的域名;
    root /usr/wwwroot;
    ssl on;
    ssl_certificate   /data/example.com/fullchain.crt;
    ssl_certificate_key  /data/example.com/example.com.key;
	ssl_ciphers                 TLS13-AES-256-GCM-SHA384:TLS13-CHACHA20-POLY1305-SHA256:TLS13-AES-128-GCM-SHA256:TLS13-AES-128-CCM-8-SHA256:TLS13-AES-128-CCM-SHA256:EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+ECDSA+AES128:EECDH+aRSA+AES128:RSA+AES128:EECDH+ECDSA+AES256:EECDH+aRSA+AES256:RSA+AES256:EECDH+ECDSA+3DES:EECDH+aRSA+3DES:RSA+3DES:!MD5;
    ssl_prefer_server_ciphers    on;
    ssl_protocols                TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_session_cache            shared:SSL:50m;
    ssl_session_timeout          1d;
    ssl_session_tickets          on;
}
EOF
```
#### 4.配置nginx服务
**创建服务文件**
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
 
**加载并启动nginx**
```
systemctl daemon-reload
```
```
systemctl enable nginx
```
```
systemctl restart nginx
```
**其他命令**
```
systemctl start nginx  -启动
systemctl stop nginx  -停止
```
### 安装伪装网站

**创建伪装网站目录**
```
mkdir /usr/wwwroot
```
**下载伪装网站**
```
git clone  https://github.com/JeannieStudio/Programming.git /usr/wwwroot
```

### 安装v2ray
**用官方快速安装脚本，地址：**[https://github.com/v2fly/fhs-install-v2ray](https://github.com/v2fly/fhs-install-v2ray)

**安装cURL**
debian或ubuntu:

```
 apt update
 apt install curl
```
centOS：

```
yum makecache
yum install curl
```
**使用一键脚本下载并安装v2ray**

```
curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh
curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-dat-release.sh
```
**安装完成以后要修改一下后台服务文件**
```
cat >/etc/systemd/system/v2ray.service <<EOF
[Unit]
Description=V2Ray Service
After=network.target nss-lookup.target

[Service]
User=root
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
NoNewPrivileges=true
Environment=V2RAY_LOCATION_ASSET=/usr/local/share/v2ray/
ExecStart=/usr/local/bin/v2ray -config /usr/local/etc/v2ray/config.json
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
```
#### 配置v2ray
参考官方给的配置即可：[https://github.com/v2fly/v2ray-examples](https://github.com/v2fly/v2ray-examples)
生成uuid的命令：
```
cat /proc/sys/kernel/random/uuid
```
配置v2ray服务器

```
cat >/usr/local/etc/v2ray/config.json <<EOF
{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "port": 443,
            "protocol": "vless",
            "settings": {
                "clients": [
                    {
                        "id": "", // 填写你的 UUID
                        "level": 0,
                        "email": "love@v2fly.org"
                    }
                ],
                "decryption": "none",
                "fallbacks": [
                    {
                        "dest": "/dev/shm/default.sock",
                        "xver": 1
                    },
                    {
                        "alpn": "h2",
                        "dest": "/dev/shm/h2.sock",
                        "xver": 1
                    }
                ]
            },
            "streamSettings": {
                "network": "tcp",
                "security": "tls",
                "tlsSettings": {
                    "alpn": [
                        "h2",
                        "http/1.1"
                    ],
                    "certificates": [
                        {
                            "certificateFile": "/path/to/fullchain.crt", // 换成你的证书，绝对路径
                            "keyFile": "/path/to/example.com.key" // 换成你的私钥，绝对路径
                        }
                    ]
                }
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom"
        }
    ]
}
EOF
```
执行命令：
```
systemctl daemon-reload
```
```
systemctl enable v2ray
```
```
systemctl restart v2ray 
```

相关命令：
```
systemctl start v2ray    启动v2ray
systemctl stop v2ray     停止v2ray
systemctl status v2ray   查看v2ray状态
```
### 客户端下载和配置
**1.win平台：**
下载Qv2ray，使用手册：这里：[https://qv2ray.net/getting-started/](https://qv2ray.net/getting-started/)

（1）下载Qv2ray: https://github.com/Qv2ray/Qv2ray/releases


windows用户下载：Qv2ray.v2.6.3.Windows-x64.7z，并解压即可。


### 打赏
![](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E6%8D%90%E5%8A%A92.png?raw=true)














