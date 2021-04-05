---
layout: post
title:  "[homeassistant]在群晖上用docker启动homeassistant"
date:   2021-04-04 20:27:00 +0800--
categories: [实践探索]
tags:   [homeassistant]
---

### 背景

上个月由于更换了群晖的关系，原来存在微联通上的homeassistant数据库被我删除了，因为homeassistant也要求更新python3.8了，索性就在群晖上用docker来安装了，性能可以得到一定提升，也和数据库放在一起，访问应该会快不少。

homeassistant官网就有[群晖NAS安装homeassistant的教程](https://www.home-assistant.io/installation/alternative#synology-nas)，我们先照着配置一下。

### docker安装homeassistant

首先在群晖里面安装docker，然后在注册表那里下载“homeassistant/home-assistant”镜像，这里我用的推荐的“stable”版本。

下载好之后就加载这个镜像，我下载得挺快的就没配置docker镜像加速。

加载镜像的时候有些配置需要按照文档配置一下，比如要加载文件夹作为这个容器的配置文件夹，网络要使用主机相同的网络，时区要改为系统时区，然后启动这个容器就可以了。

### 注意事项

如果开了防火墙的话，要打开8123端口。

Z-Wave的USB需要配置容器的USB读取。

### 配置还原

顺利启动了homeassistant之后，我开始逐渐还原我的homeassistant配置。

将我的custom_componets、themes、www、sensors文件夹直接复制过来，将我的automations.yaml、configuration.yaml、secrets.yaml、ui-lovelace.yaml也复制过来。

其中custom_componets里面有第三方商店hacs、小米音箱tts、和风天气，这几个都还可以用。

themes里面是我的浅色和深色主题。

www是ui绘图和地图的第三方插件。

sensors里面是我自己配置的传感器，其中系统资源这个配置已不适用，cpu温度传感器用的command_line在群晖上也不适用，qnap已经停了，就把这几个先删除。

automations.yaml里面是自动切换深色浅色模式的，以及更新电费电量的，这些都保留。

secrets.yaml里面的密码有些也用不上了，先不管，后面再改。

ui-lovelace.yaml先把没有了的传感器删除。

configuration.yaml是重点要改的地方，主要是重新配置数据库，这个我下一篇博客单独讲。

以上，就基本将homeassistant从树莓派迁移到了群晖的docker上。

### 参考文献

1.[Alternative](https://www.home-assistant.io/installation/alternative#synology-nas)


  
 
___




<p align = "right">2021年4月4日</p>
<p align = "right">Hang</p>

