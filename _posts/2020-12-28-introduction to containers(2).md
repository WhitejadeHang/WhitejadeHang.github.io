---
layout: post
title:  "[学习笔记]容器简介（2）--创建IBM云Container Registry Namespace"
date:   2020-12-28 08:58:00 +0800--
categories: [实践探索]
tags:   [学习笔记, cluster&container]
---

### 目标

这是课程第一周配套实验，内容包括：

* 注册IBM云账号
* 查看IBM云目录
* 查看IBM云容器注册所服务
* 创建容器注册所名字空间

### 简介

这次的目标就是在IBM云上创建一个Container Registry Namespace，方便后续实验的使用。Namespace就是用来给用户提供独立镜像空间的，在远程操作镜像时需要指明namespace.

### 创建账号

到[IBM云官网注册页面](https://cloud.ibm.com/registration)创建账户，需要提供创建邮箱。注意IBM会检测IP地址，所以如果选在中国创建的话，开着代理会创建失败。创建成功后，会自动跳转到登录界面，登录就可以了。

### 创建容器注册所名字空间

点击IBM云控制台右上角的“目录”标签，搜索“Container Registry”，点进去后点击“创建”按钮，我们用Lite版是免费的，点击下面名称空间，点创建，输入名字即可。

在进行后续实验的时候要注意，需要在右上角“管理”界面点击“访问权（IAM）”，然后在“API密钥”处创建一个密钥，用于ibmcloud命令行登录：

```shell
ibmcloud login --apikey $IBMCLOUD_API_KEY
```

这里的$IBMCLOUD_API_KEY替换为自己的密钥即可，密钥记得保管好，只会显示一次。

通过以上配置后，我成功创建、运行了镜像，并将镜像传到了我的账户空间中，这部分是课程配套实验交互式进行的，就不在博客中列举了。
___




<p align = "right">2020年12月28日</p>
<p align = "right">Hang</p>

