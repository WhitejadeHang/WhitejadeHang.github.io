---
layout: post
title:  "[homeassistant]homeassistant数据库迁移到mariadb"
date:   2021-04-05 20:18:00 +0800--
categories: [实践探索]
tags:   [homeassistant]
---

### 背景

接着昨天将homeassistant从树莓派迁移到群晖docker上的工作，今天来将数据库配置起来。

### 数据库准备

前期我已经在群晖上安装好了mariadb，那么我们直接在上面创建一个homeassistant用的数据库。

在连接前，要给这个数据库添加远程访问权限：

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION
```

然后刷新权限：

```sql
FLUSH PRIVILEGES
```

然后在recoder中配置远程连接：

```sql
mysql://用户名:密码@主机名:端口/数据库名?charset=utf8mb4
```

配置好之后就可以使用了，后续还要配置一些细节。


### 参考文献

1.[Recorder](https://www.home-assistant.io/integrations/recorder)


  
 
___




<p align = "right">2021年4月5日</p>
<p align = "right">Hang</p>

