
---
title: "最坚挺的出国方式：v2ray+CDN+Ngnix+Websocket"
date: 2020-02-15T12:45:29+08:00
description: 这种方式最坚挺，相信我……
---


## 一、开启BBR

**Debian9 自带BBR，所以可以直接通过参数开启BBR加速。打开/etc/sysctl.conf，添加以下2行代码：**

说明：作者习惯用windows连接工具登录到vps进行修改，下面链接是WINSCP连接VPS的教程，喜欢的小伙伴可以去看看。
  [winscp工具使用教程](https://jeanniestudio.github.io/2020/02/14/%E4%BD%BF%E7%94%A8WINSCP%E8%BF%9E%E6%8E%A5VPS%E6%95%99%E7%A8%8B--%E4%B8%80%E9%94%AE%E8%84%9A%E6%9C%AC/)
  <!-- more -->
```
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
```
**添加并保存，完成之后别忘了执行以下命令重启：**

```
sysctl -p
```
## 二、安装v2ray

**首先打开 vm 实例的 ssh 终端，依次执行以下命令：**

*1.切换成root用户*
```
 sudo -i
```
*2.查看当前时间*
```
date -R
```
*3.同步本地时间* 
```
 cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

*4.执行一键安装脚本*
```
 bash <(curl -L -s https://install.direct/go.sh)
```

**终端显示”V2Ray v4.22.1 is installed.”，表示安装完成。**
**v2ray相关命令：**

| 启动：  | service v2ray start |
| 停止：      | service v2ray stop |
|查看状态：  | service v2ray status |
| 重启： | service v2ray restart |
| 重载： |service v2ray reload  |
| 强行重载： | service v2ray force-reload |



##  三、安装Ngnix 

*安装命令：*
```
 apt-get install nginx
```
*重启Ngnix命令：*
```
systemctl restart nginx
```
*检查Ngnix状态命令：*

```
systemctl status nginx
```
打开浏览器，访问vps的ip地址，出现欢迎页面。

Nginx 配置文件目录： /etc/nginx/sites-available 
Nginx 默认网页文件目录/var/www/html 存放着 Nginx 的默认主页（欢迎页）

**将/etc/nginx/sites-available 的defaut里面的内容全部删除，复制下面这段放到default中。
访问自己的域名，出现欢迎页面即说明解析成功。**

```
server {

        listen 80 default_server;

        listen [::]:80 default_server;
       

        server_name mydomain.me; #这里修改成自己的域名即可。

        location / {

                # First attempt to serve request as file, then

                # as directory, then fall back to displaying a 404.

                try_files $uri $uri/ =404;

        }

        }

```



##  四、 TLS证书


### 准备工作

 证书的生成有许多方法，这里使用的是比较简单的方法：使用 acme.sh 脚本生成。
 
 **注意：针对debian9系统，在执行安装acme.sh脚本之前，需要提前安装必备软件包。不然会有提示"It is recommended to install socat first"等错误信息提示。**
 
*必备软件包安装执行命令：*

 ```
apt-get update && apt-get install curl -y && apt-get install cron -y && apt-get install socat -y
```
### 安装 acme.sh

*执行以下命令：*

```
curl  https://get.acme.sh | sh
```

*安装成功后执行 以下命令，以确保脚本所设置的命令别名生效：*
```
source ~/.bashrc
```

如果安装报错，那么可能是因为系统缺少 acme.sh 所需要的依赖项，acme.sh 的依赖项主要是 netcat(nc)，我们通过以下命令来安装这些依赖项，然后重新安装一遍 acme.sh:**（注：debian9在安装过程中没有报错，所以这步不需要执行）**
 
```
 apt-get -y install netcat
```

以下的命令会临时监听 80 端口，而80端口被Ngnix服务占用，所以要暂停Ngnix服务：

```
systemctl stop nginx  
```


### 生成证书（注意：脚本中的域名改成自己的）

```
~/.acme.sh/acme.sh --issue -d mydomain.me --standalone -k ec-256
```
**证书自动生成到 /etc/v2ray/ 文件夹，可以自行查阅。**

###  证书更新（脚本自动）

由于 Let's Encrypt 的证书有效期只有 3 个月，因此需要 90 天至少要更新一次证书，acme.sh 脚本会每 60 天自动更新证书。所以这步不需要做。**（但如果你不放心，也把手动更新的指令提供一下，供参考。）**


*手动更新 ECC 证书，执行：*
```
~/.acme.sh/acme.sh --renew -d mydomain.com --force --ecc
```

*如果是 RSA 证书则执行：*
```
sudo ~/.acme.sh/acme.sh --renew -d mydomain.com --force
```

由于本例中将证书生成到 /etc/v2ray/ 文件夹，更新证书之后还得把新证书生成到 /etc/v2ray。


### 安装证书和密钥（ECC和RSA二选一）

ECC优点：同等长度 ECC 比 RSA 更安全
ECC缺点：兼容性会差一些，Android 4.x 以下和 Windows XP 不支持。

**只要您的设备不是非常老的老古董，建议使用 ECC 证书。**

*ECC 证书将证书和密钥安装到 /etc/v2ray 中执行：（注意：脚本中的域名改成自己的）*
```
$ sudo ~/.acme.sh/acme.sh --installcert -d mydomain.me --fullchainpath /etc/v2ray/v2ray.crt --keypath /etc/v2ray/v2ray.key --ecc
```

*RSA 证书执行：（这条列出来供参考，别执行）*
```
$ sudo ~/.acme.sh/acme.sh --installcert -d mydomain.me --fullchainpath /etc/v2ray/v2ray.crt --keypath /etc/v2ray/v2ray.key
```

**注意：无论什么情况，密钥(即上面的v2ray.key)都不能泄漏，如果你不幸泄漏了密钥，可以使用 acme.sh 将原证书吊销，再生成新的证书，吊销方法请自行参考 acme.sh 的手册。**


## 五、配置

### v2ray服务端

最终v2ray服务端的配置只需要复制下面这一段，把/etc/v2ray下面的config.json里的内容全部删除，用这一段替换。注意有些需要修改成自己的（**port、id、alterid、path需要修改**）

```
{
  "inbounds": [
    {
      "port": 10000,  //这个端口自己修改一下
      "listen":"127.0.0.1",//只监听 127.0.0.1，避免除本机外的机器探测到开放了 10000 端口
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "b831381d-6324-4d53-ad4f-8cda48b30811", //id自行修改，但保证位数一致
            "alterId": 64 //自行修改
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
        "path": "/ray/"       //注意：这里也改成自己的，2条斜杠别漏了
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```
###  Ngnix配置

将/etc/nginx/sites-available 的defaut里面的内容全部删除，复制下面这段放到default中。
注意：需要修改location和proxy_pass这两项，和v2ray服务端保持一致。
```
server {
  listen  443 ssl;
  ssl on;
  ssl_certificate       /etc/v2ray/v2ray.crt;
  ssl_certificate_key   /etc/v2ray/v2ray.key;
  ssl_protocols         TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers           HIGH:!aNULL:!MD5;
  server_name           mydomain.me;
        location /ray/ { # 与 V2Ray服务端 配置中的 path 保持一致
        proxy_redirect off;
        proxy_pass http://127.0.0.1:10000;#这个端口和服务端保持一致
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;

        # Show realip in v2ray access.log
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
}
```
**注意：配置完成后别忘了重启v2ray和ngnix，上面有重启命令。**

## 六、快乐出国

### PC端：

v2ray客户端我们选择v2rayN（win64为例）

**下载 v2rayN-Core**
下载链接：[https://github.com/2dust/v2rayN/releases/latest](https://github.com/2dust/v2rayN/releases/latest)

然后选择 v2rayN-Core.zip 下载,下载完成后，按如下步骤进行配置：

1. 解压。
2. 运行v2rayN.exe。
3. 双击v2rayN图标，打开图形界面，点击“服务器”--“添加[VMess]服务器”
4. 按下图配置客户端，填完后保存。

![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/v001.jpg?raw=true)

5. 然后在v2ray图标上点击右键，选择开启http代理。大功告成！！！


### iOS客户端：

ios客户端有：

1. Kitsuneb，目前售价$4.99；

2. Quantumult，目前售价$3.99。

作者用的是Quantumult，某宝上有兑换码，搜关键字：Quantumult 兑换码就出来了，售价2.99人民币，作者用了个某宝红包，最后只花了8毛钱搞定。

**注意：App Store账号必须是美区的id。**

### 安卓客户端：

安卓客户端使用v2rayNG：
项目地址 : https://github.com/2dust/v2rayNG/releases
这是工具是免费的。

## 问题解决

全部过程搭建好以后，频繁报下面这个错误：
```
v2ray.com/core/app/proxyman/outbound: failed to process outbound traffic > v2ray.com/core/proxy/vmess/outbound: failed to find an available destination > v2ray.com/core/common/retry: [v2ray.com/core/transport/internet/websocket: failed to dial WebSocket > v2ray.com/core/transport/internet/websocket: failed to dial to (wss://我的域名/download/):  > dial tcp 这里是ip地址:443: i/o timeout v2ray.com/core/transport/internet/websocket: failed to dial WebSocket > v2ray.com/core/transport/internet/websocket: failed to dial to (wss://我的域名/download/):  > dial tcp: operation was canceled] > v2ray.com/core/common/retry: all retry attempts failed
```

我的解决方法：将cloudflare里的cdn关闭就可以了。理论上cdn是加速的，但可能是cloudflare的服务器是国外的导致的。

## 捐助本站

![二维码](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E6%8D%90%E5%8A%A92.png?raw=true)





