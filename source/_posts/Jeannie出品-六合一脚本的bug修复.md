---
title: 六合一脚本的bug修复
date: 2020-04-22 05:42:30
tags:
description: 修复重大bug
---
### 前言
网友反馈一个重大bug，执行定时任务后由于获取不到证书的到期日，造成死循环，故造成cpu利用率持续100%

### 修复执行如下代码
切换到root用户：

```
sudo -i
```

执行一键搭建脚本：

```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/JeannieStudio/jeannie/master/bug.sh)"
```

**视频链接：**
**订阅本频道：https://bit.ly/2X042ea**
