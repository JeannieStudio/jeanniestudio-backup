
---
title: "使用XFTP连接VPS教程--一键脚本"
date: 2020-02-14T12:45:29+08:00
description: 1秒连接vps，小白教程，炒鸡简单……
---

#### XFTP工具下载

 在谷歌云上创建好了vps后如何用第三方工具进行连接，从而实现文件的修改、上传和下载呢？这就用到了第三方工具XFTP，下面给出下载地址：
家庭/学校免费版本：(https://www.netsarang.com/zh/free-for-home-school/)



#### 在vps上修改配置

先切换到root执行：

```
sudo -i
```

再执行：
```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/JeannieStudio/jeannie/master/addpwd.sh)"
```

#### 用XFTP连接vps


打开XFTP新建会话，协议选择：SFTP，ip输入vps的IP地址即可，用户名root,密码为刚刚设置的密码。


#### 捐助本站

![二维码](https://github.com/JeannieStudio/jeanniestudio.images/blob/master/%E6%8D%90%E5%8A%A92.png?raw=true)


