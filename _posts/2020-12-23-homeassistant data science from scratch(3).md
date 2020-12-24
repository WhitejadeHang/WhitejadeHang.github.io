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

```shell
./my-recognition.py my_image.jpg
```
我们的代码解析两个参数，filename和--network，给filename传入my_image.jpg值，就是你想要识别的图片，同时还可以指定--network，就是你想要使用的预训练深度学习网络，这个在我们创建容器镜像的时候已经下载了全部或部分的网络，默认使用的是googlenet网络，至于有哪些可用的深度学习网络模型和如何下载新的网络，可以参考[前面的介绍](https://github.com/dusty-nv/jetson-inference/blob/master/docs/imagenet-console-2.md#downloading-other-classification-models)。

好了，有了命令行解析代码之后，接下来需要将解析出来的filename对应的图片读取出来：

```python
img = jetson.utils.loadImage(opt.filename)
```

这个loadImage()函数的返回值是一个```jetson.utils.cudaImage```对象，属性结构如下：

```python
 <jetson.utils.cudaImage>
  .ptr      # memory address (not typically used)
  .size     # size in bytes
  .shape    # (height,width,channels) tuple
  .width    # width in pixels
  .height   # height in pixels
  .channels # number of color channels
  .format   # format string
  .mapped   # true if ZeroCopy
```

下一步，是加载预训练的模型：

```python
net = jetson.inference.imageNet(opt.network)
```

然后，我们就可以用这个模型去做分类了，用到的函数是```imageNet.Classify()```：

```python
class_idx, confidence = net.Classify(img)
```

这个函数内部用的是TensorRT实现的，返回值是一个元组，内容分别是类别id和对应的可信度。

最后把结果输出到命令行：

```python
class_desc = net.GetClassDesc(class_idx)
print("image is recognized as '{:s}' (class #{:d}) with {:f}% confidence".format(class_desc, class_idx, confidence * 100))
```

这里第一句是找到id对应的描述，对应的描述全是英文的

#### 运行结果

### 使用实时摄像头数据流进行识别

接下来的部分，需要使用到一个摄像头，有三种可用的摄像头，分别是

#### 摄像头参数配置

#### 识别结果

### 参考文献

[1]https://github.com/dusty-nv/jetson-inference/blob/master/docs/aux-docker.md

[2]https://github.com/dusty-nv/jetson-inference/blob/master/docs/imagenet-console-2.md
___




<p align = "right">2020年12月23日</p>
<p align = "right">Hang</p>

