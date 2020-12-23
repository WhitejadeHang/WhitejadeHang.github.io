---
layout: post
title:  "[homeassistant]从0开始的homeassistant数据科学（3）--Jetson图像识别编程与视频流的配置"
date:   2020-12-23 15:11:00 +0800--
categories: [实践探索]
tags:   [homeassistant]
---

### 自己用Python编写图像识别程序

上一篇介绍了简单的图像识别应用，这次我们自己来创建一个图像识别的python脚本，加深一下印象。

说是自己编实际上识别部分还是用的imageNet类，也就从代码层面简单熟悉一下图像识别应用的流程啦。

#### 创建工程目录

我们的程序准备在宿主机上用vscode编写的，然后在docker容器里面运行，所以最好能把我们编好的代码直接挂载到容器上。

上一篇文章我们讲到了四个目录是会自动挂载的，那么我们就在其jetson-inference/python/training/classification/data这个目录下创建我们的工程：

```shell
cd jetson-inference/python/training/classification/data
mkdir my-recognition-python
cd my-recognition-python
touch my-recognition.py
chmod +x my-recognition.py
```

以上命令分别创建了工程文件夹、脚本文件并添加了脚本的执行权限。

然后弄几张测试图片进去，可以本地考自己的图片，也可以在网上随便下载几张：

```shell
wget https://github.com/dusty-nv/jetson-inference/raw/master/data/images/black_bear.jpg 
wget https://github.com/dusty-nv/jetson-inference/raw/master/data/images/brown_bear.jpg
wget https://github.com/dusty-nv/jetson-inference/raw/master/data/images/polar_bear.jpg 
```
#### 代码解读

在启动了docker容器后，仍然可以在宿主机对代码进行编辑，首先要在脚本文件的开头添加：

```python
#!/usr/bin/python3
```

指定脚本使用python3作为解释器。

然后要导入python模组：

```python
import jetson.inference
import jetson.utils

import argparse
```

其中```jetson.inference```和```jetson.utils```用于图像识别和图像加载，```argparse```用于命令行解析。

然后下面这段代码就是解析命令行的：

```python
parser = argparse.ArgumentParser()
parser.add_argument("filename", type=str, help="filename of the image to process")
parser.add_argument("--network", type=str, default="googlenet", help="model to use, can be:  googlenet, resnet-18, ect. (see --help for others)")
opt = parser.parse_args()
```

我们编的这个简单程序对用户指定的图像进行识别，预期运行是这样的：

#### 运行结果

### 使用实时摄像头数据流进行识别

#### 摄像头参数配置

#### 识别结果

### 参考文献

[1]https://github.com/dusty-nv/jetson-inference/blob/master/docs/aux-docker.md

[2]https://github.com/dusty-nv/jetson-inference/blob/master/docs/imagenet-console-2.md
___




<p align = "right">2020年12月23日</p>
<p align = "right">Hang</p>

