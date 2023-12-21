---
title: Jeannie出品-针对v2ray core的重大安全漏洞，进行6合1脚本的更新
date: 2020-06-01 16:42:30
tags:
description: 速度与安全并存，一键搭建超级简单
---
### 前言
**针对v2ray在v4.23.2 前固定 TLS 密码套件潜在的特征识别风险，对6合1脚本做相应更新**
<iframe width="560" height="315" src="https://www.youtube.com/embed/cNSq5RdVf28" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


### 情况1
**如果已经安装了6合一脚本中的3或者4，请执行下面两句进行v2ray-core的更新：**

```
bash <(curl -L -s https://install.direct/go.sh)
```

```
service v2ray restart
```

### 情况2
**如果在这个视频发布之后安装的即是最新版本，无需更新：**

### 本次对6合1脚本的更新

**管理脚本：/etc/all_mgr.sh中添加数字8：更新v2ray core，以后可以随时更新v2ray core**



**视频链接：https://youtu.be/cNSq5RdVf28**
**订阅本频道：https://bit.ly/2X042ea**