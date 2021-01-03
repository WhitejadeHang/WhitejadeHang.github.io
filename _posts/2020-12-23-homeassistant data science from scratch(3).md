---
layout: post
title:  "[Deep Learning]深度学习与数据科学（2）--Jetson图像识别编程与视频流的配置"
date:   2020-12-23 15:11:00 +0800--
categories: [实践探索]
tags:   [经验记录, 深度学习, Jetson]
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
  .ptr      # 地址
  .size     # 图片大小
  .shape    # (height,width,channels) 图片形状
  .width    # 像素宽度
  .height   # 像素高度
  .channels # 色彩通道数量
  .format   # 格式
  .mapped   # 映射数量（没有复本就是0）
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

这里第一句是找到id对应的描述，对应的描述是英文的，从一个ilsvrc12_synset_words.txt文本里加载的，如果需要汉化的话，可以用谷歌翻译处理一下。

#### 运行结果

以上就是一个最简单的图片识别程序，完整代码如下：

```python
 #!/usr/bin/python3

import jetson.inference
import jetson.utils

import argparse

 # 命令行解析
parser = argparse.ArgumentParser()
parser.add_argument("filename", type=str, help="filename of the image to process")
parser.add_argument("--network", type=str, default="googlenet", help="model to use, can be:  googlenet, resnet-18, ect.")
args = parser.parse_args()

 # 加载图片
img = jetson.utils.loadImage(args.filename)

 # 加载识别网络
net = jetson.inference.imageNet(args.network)

 # 图片分类
class_idx, confidence = net.Classify(img)

 # 获取图片的描述
class_desc = net.GetClassDesc(class_idx)

 # 打印结果
print("image is recognized as '{:s}' (class #{:d}) with {:f}% confidence".format(class_desc, class_idx, confidence * 100))
```
接下来可以用：

```shell
./my-recognition.py polar_bear.jpg
```
来看看程序执行结果，我这里用这条命令出错了，而改用：

```shell
python3 my-recognition.py polar_bear.jpg
```

则成功输出了结果：

![识别结果](/images/20201223/recongres.png)

### 使用实时摄像头数据流进行识别

接下来的部分，需要使用到一个摄像头，有三种可用的摄像头，分别是MIPI CSI摄像头（树莓派那种），V4L2摄像头（USB那种）还有网络摄像头（通过IP配置）。

我自己用的是配置最简单的USB摄像头，其他种类的这里就不介绍如何配置了。

USB摄像头使用的是V4L2协议，在linux下将被识别为/dev/文件夹下的一个文件，通常叫video0或video1，可以在插入摄像头后使用ls查看一下有没有。

#### 摄像头参数配置

摄像头存在的话，可以在容器里用video-viewer查看，以下4条命令都可以调用这个摄像头：

```shell
video-viewer v4l2:///dev/video0                 # /dev/video0 可以替换为 /dev/video1之类的.
video-viewer /dev/video0                        # 去掉开头的 v4l2:// protocol 协议也可以
video-viewer /dev/video0 output.mp4             # 输出保存为 MP4 文件 (默认为H.264格式)
video-viewer /dev/video0 rtp://<remote-ip>:1234 # 讲结果广播到远程 <remote-ip>
```

可以指定视频的宽度、高度、编码方式，像这样：

```shell
video-viewer --input-width=1920 --input-height=1080 --input-codec=h264 /dev/video0
```

当我准备用：```./imagenet.py /dev/video0```执行的时候却出了问题，使用：

```shell
v4l2-ctl --device=/dev/video0 --list-formats-ext
```

查看发现，原来我这个摄像头是YUYV格式的，程序没有支持，修改了各种参数都没成功。好在我还有一个安卓手机，果断安装ip摄像机，详细配置方法可以参考[这篇文章](https://www.appinn.com/surveillance-station-with-android-ip-camera/)，然后按照app提示，输出h.264编码的视屏流：

```
./imagenet --input-codec=h264 rtsp://your.ip.address.and:port/h264_pcm.sdp
```

#### 识别结果

识别的效果如图：

![liveres](/images/20201223/liveres.png)

可以尝试修改一下运行参数，如指定视屏的大小，还有调低帧率（30帧结果就闪得太快了），还可以将结果输出：

```shell
./imagenet --input-width=720 --input-height=480 --input-codec=h264 --input-rate=10 rtsp://your.ip.address.and:port/h264_pcm.sdp output.mp4
```

还有就是结果没汉化看起来有点难受，后面还要用到的时候我就拉出来谷歌翻译一份。googelnet的动态识别有点不太行，后面得用更好的模型。

以上就是英伟达Jetson系列图像识别的简单教程，后面我还会继续完成这个系列教程的目标检测和图像分割部分。另外就是最近在学习Docker和Kubernetes，后续会用容器技术在这个Jetson Xavier NX上开展正式的电力数据分析。

___




<p align = "right">2020年12月23日</p>
<p align = "right">Hang</p>

