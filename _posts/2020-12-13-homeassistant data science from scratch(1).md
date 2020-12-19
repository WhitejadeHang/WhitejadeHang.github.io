---
layout: post
title:  "[homeassistant]从0开始的homeassistant数据科学（1）--配置Jetson Xavier NX"
date:   2020-12-13 20:00:00 +0800--
categories: [实践探索]
tags:   [homeassistant]
---

### 前言

我的上一篇博客是在3个多月前了，2020的下半年，我确实有很多做得不够好的地方。

现在，在2020的尾巴，让我说一句：“接下来加倍努力！”吧，每个方面都要。

所以，七月的从零开始的homeassistant数据科学就让我继续吧，never too late。

这篇文章要介绍的是我后面将用来做数据分析的主力工具：Jetson Xavier NX。

### Jetson Xavier NX系统烧写与配置

#### 系统烧写
Jteson Xavier NX是英伟达的一款嵌入式AI开发板（模块），我是直接在英伟达的[Jetson下载中心](https://developer.nvidia.com/zh-cn/embedded/downloads)获取的SD卡镜像，准备了一张256G的SD卡进行镜像烧写。

镜像自带Jetpack软件包，本机开发比较方便，自带docker，做虚拟环境也很方便。

#### 代理与换源

官方系统版本是ubuntu18.04LTS，代理配置直接可以在图形界面完成，相比树莓派只能命令行配置要方便一点。

换源方面，编辑/etc/apt/sources.list，我用的是清华源，替换文件内容为：

```yaml
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-updates main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-security main restricted universe multiverse
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-security main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-backports main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic main universe restricted
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic main universe restricted
```

然后

```shell
sudo apt-get update
```

更新源即可。

此外还需更换pip源，在主文件目录~/下创建.pip文件夹，然后创建pip.conf文件，输入内容：

```yaml
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=mirrors.aliyun.com
```

即可，可以使用：

```shell
pip config list
```

查看当前使用的pip源。完成了换源后，在国内使用会方便不少。

#### cuda环境配置

本机开发需要配置cuda环境变量，修改~/.bashrc，在环境变量文件最后加入：

```yaml
export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
export CUDA_HOME=$CUDA_HOME:/usr/local/cuda
```

然后执行：

```shell
source ~/.bashrc
```

刷新环境变量。

可以使用：

```shell
nvcc -V
```

查看cuda环境变量是否配置成功。

#### Jtop安装

jtop是jetson系列的资源监视软件，比较方便，推荐安装一个。

首先要确保安装了pip3:

```shell
sudo apt-get install python3-pip
```

然后：

```yaml
sudo -H pip install jetson-stats
```

即可，现在运行jtop已不需要sudo，直接在命令行输入jtop就行了。

### 配置VScode集成开发环境

vscode是一个好用的集成开发环境，这里介绍两种安装方式，第一种是在github下载软件，这里我们用curl下载：

```shell
curl -o /usr/bin/v2-ui -Ls https://raw.githubusercontent.com/sprov065/v2-ui/master/v2-ui.sh
```

然后使用：

```shell
sudo dpkg -i code-oss_1.32.3-arm64.deb
```

安装软件包。

第二种是直接在ububtu的商店里面搜索code-oss下载即可。

安装好之后，在创建新的工程的时候注意，先安装python插件，然后在新工程文件夹内创建.vscode文件夹，然后创建settings.json文件，输入：

```json
{
    "python.linting.pylintArgs": ["--generate-members", "--extension-pkg-whitelist=cv2"],
    "python.autoComplete.extraPaths": ["/usr/lib/python3.6/dist-packages/cv2/python-3.6"],
    "python.pythonPath": "/usr/bin/python3",
    "editor.fontSize": 20
}
```
文件内容，使得其自动补全功能可以生效。

### 添加nvme存储

xavier nx是支持nvme扩展的，正好我也有1T的WD nvme卡，开始我打算直接弄成nvme上启动，按照[github上的一个方法](git clone https://github.com/jetsonhacks/rootOnNVMe)没有成功，索性退而求其次，就将nvme挂载成一块数据盘吧。

装上nvme后，可以在图形界面使用系统自带的磁盘管理工具查看到硬件。

将这块盘格式化伪ext4格式（这是当系统盘需要的，做数据盘可能其他格式也可以），然后挂载起来，这时可以在磁盘工具看到硬件的设备文件地址和挂载到的位置。

我们的目标是将这个存储设备开机自动挂载到指定位置（我在根目录下创建了/data文件夹），需要使用：

```shell
sudo blkid
```

查看存储块的id，把nvme设备文件地址对应的id记下来，在/etc/fstab文件中添加：

```yaml
UUID=你的id  /data ext4 defaults 0 2 
```

这里UUID就是刚刚记录的id，/data是挂载地址，ext4是文件格式, 0代表不备份。

完成后重启，nvme就会作为数据盘一直挂载起来了。

### 连接github帐号

首先配置系统的git用户信息：

```shell
git config --global user.name "XX"
git config --global user.email XX@XX
```

然后，生成SSH Keys:

```shell
ssh-keygen -t rsa -c "XX@XX"
```

系统会提示key的保存位置（一般在~/.ssh目录），还会让输入口令，直接回车可以指定空的口令。

然后将保存的公钥内容粘贴到github添加SSH key的界面中（github->Settings->SSH and GPG Keys->New SSH key）

最后可以通过：

```shell
ssh -T git@github.com
```

测试帐号是否连接成功。
 
___




<p align = "right">2020年12月13日</p>
<p align = "right">Hang</p>

