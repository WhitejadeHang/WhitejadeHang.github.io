---
layout: post
title:  "[homeassistant]从0开始的homeassistant数据科学（1）--在NAS上用docker搭建开发环境"
date:   2021-04-27 22:57:00 +0800--
categories: [实践探索]
tags:   [homeassistant,data science]
---

### 前言

终于在homeassistant数据科学取得了一点点进展了！

在经过了8个月后，我们的从0开始的homeassistant数据科学更新到第一集了。

这次要写的是我花了一个周末完成的在NAS上搭建了jupyterlab+tensorflow+hassdatadetective环境，以及准备对ha官网推荐的数据分析库hassdatadetective进行改进的计划，改进的实施就可以放在下一期了。

### 后台数据库

我们在第零集里说到了ha的recorder组件，在将ha迁移到docker里之后，相关的环境是齐全的，只需要按照文档配置好数据库url即可，根据所使用的数据库，在hassdatadetective里使用时需要注意，默认的sqlite也是可以的。

### docker镜像选择

docker镜像方面，开始我选择的是jupyter-lab镜像以及jupyter-lab tensorflow镜像。但是这个镜像自带的用户是没有相关信息和权限的，而且jupyter-lab tensorflow镜像自带的tensorflow因为NAS所带的cpu比较旧款没有AVX支持所以也不能用，所以最后我选择的是最简单的ubuntu20.04镜像所有东西都自己搭建。

### 安装conda

前面说到tensorflow因为NAS所带的cpu比较旧款没有AVX支持不能用，这对我们后续使用深度学习分析，如果我们直接用pip安装tensorflow也会出现同样的问题。这里最简单的解决方案就是使用conda，conda会选择适合系统的与编译软件。可以在miniconda官网或者[清华镜像](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/)下载软件。

`
wget -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-4.6.14-Linux-x86_64.sh
`

然后使用

`
bash Miniconda3-4.6.14-Linux-x86_64.sh
`

### 安装jupyter-lab和tensorflow

完成安装后，可以使用conda安装jupyter-lab和tensorflow了。

使用conda安装就没有什么难度了。直接使用：

`
conda install jupyter-lab
`

和

`
conda install tensorflow
`

即可。

### 安装hassdatadetective

有了jupyter-lab和tensorflow，我们现在需要的是将ha的后台数据与jupyter-lab的开发平台以及tensorflow的分析能力结合起来。

对hassdatadetective的安装就在其[github仓库](https://github.com/robmarkcole/HASS-data-detective#simple-query)下载文件夹，然后在包含setup.py的文件夹下运行：

`
pip install .
`

即可完成。但到这一步了还不能真正使用这个包，因为我的数据库已经更换成了MariaDB,所以还要安装使用python3调用MySQL的包，这里要注意不要安装python2的。对python3来说，直接：

`
pip3 install mysqlclient
`

即可。至此，使用ha进行数据科学的基本环境就搭建完成了。

### hassdatadetective改进

hassdatadetective这个包目前还存在不少的问题，主要是该包的实践检验较少，还有开发者所采集的数据种类和频次不多，应用场景需求不明确。

目前，我的ha在数据方面已经有百万级的数据积累了，实体也有超过100个了，需求方面，主要是以时间序列的方式建立各类数据的状态-事件对应关系，我将在后续对需求和应用场景进行详细梳理。


### 参考文献

1.[miniconda清华镜像](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/)

2.[hassdatadetectivegithub仓库](https://github.com/robmarkcole/HASS-data-detective#simple-query)


  
 
___




<p align = "right">2021年4月5日</p>
<p align = "right">Hang</p>

