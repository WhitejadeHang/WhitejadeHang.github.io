---
layout: post
title:  "[homeassistant]树莓派4B4G版Raspbian buster10 32位系统安装homeassistant"
date:   2020-04-11 21:29:00 +0800--
categories: [实践探索]
tags:   [homeassistant, 经验记录]
---

使用buster10 系统是因为 intel compute stick 2 兼容
所以希望能在这目前最高配置的树莓派上安装成功,查看系统:
```shell
cat /etc/os-release
```

## 1.	配置cli代理
采用笔记本v2ray代理shell，保证依赖软件顺利安装，不然非常容易卡住。
```shell
export http_proxy=http://192.168.xx.xx:xxxx
export https_proxy=http://192.168.xx.xx:xxxx
```
使得所有http走代理，使用`curl ip.sb`测试是否成功，仅对当前cli有效。

## 2.	安装python虚拟环境
使用虚拟环境管理依赖是比较科学的办法，不然久了肯定会互相冲突
首先更新一下系统：
```shell
sudo apt-get update 
sudo apt-get upgrade –y
```
安装相关依赖:
```shell
sudo apt-get install python3 python3-dev python3-venv python3-pip libffi-dev libssl-dev
```
然后:
```shell
python3 -m venv homeassistant
cd homeassistant
```
进入虚拟环境：
```shell
source bin/activate
```
退出虚拟环境：
```shell
deactivate
```
在虚拟环境中安装一些工具：
```shell
python3 -m pip install wheel
```
## 3.	安装ha
```shell
python3 -m pip install homeassistant
```
安装过程中出现错误，采用升级的方法解决：
```shell
python3 -m pip install --upgrade homeassistant（可指定版本号==x.xx.x）
```
显示安装成功后启动：
```shell
hass --open-ui
```
首次启动又会安装很多依赖：
其中遇到的问题：
```shell
Unable to install package hass-nabucasa==0.31
Setup failed for cloud: Requirements for cloud not found: ['hass-nabucasa==0.31'].
OpenSSL.SSL.WantReadError
Unable to install package home-assistant-frontend==20200130.3	
Setup failed for frontend: Requirements for frontend not found: ['home-assistant-frontend==20200130.3'].
```
hass-nabucasa可以通过pip安装
Pip安装指定版本的软件包
```shell
pip install hass-nabucasa==0.31
```
对于home-assistant-frontend==20200130.3安装：
```shell
python3 -m pip install home-assistant-frontend==20200130.3
```
网络时好时坏，好的时候一下就成功了
退出代理，运行：
```shell
hass --open-ui
```
到此，ha安装完成，可以在局域网内访：
http://ipaddress:8123/

  
 
___




<p align = "right">2020年4月11日</p>
<p align = "right">于晴朗的仲春之夜</p>
<p align = "right">Hang</p>

