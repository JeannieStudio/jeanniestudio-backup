---
title: "如何查看linux系统内核版本和发型版本？"
date: 2020-02-19T10:35:10+08:00
description: 看完本文再不理解这俩内核的关系，来找我……
---

## 前言


**linux内核是系统的心脏，可以将内核比喻成：汽车的发动。**

**可以将发型版本比喻成：发动机+轮胎+外壳+……组成的不同品牌的汽车。**

**比如下图，大众CFE发动机+其他零部件可以组装成速腾、高尔夫、朗逸、宝来等汽车。**

![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/%E5%8F%91%E5%8A%A8%E6%9C%BA%E6%AF%94%E5%96%BB.jpg?raw=true)

**同样，linux内核+其他应用程序组成了Redhat、Debian、CentOS、ubuntu。**

![如图](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E8%B0%B7%E6%AD%8C%E4%BA%91/linux%E6%AF%94%E5%96%BB.jpg?raw=true)



## 查看Linux内核版本命令

**命令1：**  ```cat /proc/version```

**显示：**

```
Linux version 4.14.129-bbrplus (root@vultr.guest) (gcc version 6.3.0 20170516 (Debian 6.3.0-18+deb9u1)) #1 SMP Mon Jun 24 16:51:05 UTC 2019
```

**命令2：**  ```uname -a```

**显示：**

```
Linux trojan001 4.9.0-12-amd64 #1 SMP Debian 4.9.210-1 (2020-01-20) x86_64 GNU/Linux
```


**命令3：**  ```uname -r```

```
4.9.0-12-amd64
```


## 查看Linux发型版本信息

**命令1：** ```cat /etc/issue```

**显示：**
```
Debian GNU/Linux 9 \n \l
```

**命令2：**``` cat /etc/lsb-release```

**显示：**
```
Distributor ID: Debian
Description:    Debian GNU/Linux 9.12 (stretch)
Release:        9.12
Codename:       stretch
```
## 捐助本站

![二维码](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E6%8D%90%E5%8A%A92.png?raw=true)

