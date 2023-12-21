---
title: "windows传文件到linux"
date: 2020-02-21T00:29:12+08:00
description: 
---
#### 打开cmd,执行
修改/etc/ssh/sshd_config文件的
PasswordAuthentication no改为yes

使用如下指令重启 SSH 服务：
service sshd restart

#### 打开cmd,执行

```
pscp -P 22 trojan-go_install.sh root@34.123.143.11:/root
```

