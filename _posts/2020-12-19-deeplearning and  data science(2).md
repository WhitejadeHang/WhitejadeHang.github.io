---
layout: post
title:  "[Deep Learning]深度学习与数据科学（1）--Jetson AI 入门"
date:   2020-12-19 17:10:00 +0800--
categories: [实践探索]
tags:   [经验记录, 深度学习, Jetson]
---

### Hello AI World

上一篇介绍了基本的Jetson Xavier NX环境配置，今天接着介绍Github上一个部署人工智能的学习仓库[jetson-inference](https://github.com/dusty-nv/jetson-inference)。

这是一个比较好的可实践的学习教程，支持Jetson 家族从Nano到AGX Xavier全套设备，使用的框架是英伟达的TensorRT。

### 使用Docker运行

这个项目有配套的Docker镜像，如此方便，当然要用起来啦，使用如下的命令可以自动获取当前jetPack版本对应的镜像：

```shell
git clone --recursive https://github.com/dusty-nv/jetson-inference
cd jetson-inference
docker/run.sh
```

同时，以下文件目录会自动挂载到镜像：

```
jetson-inference/data(存储网络模型、序列化TensorRT引擎和测试图片)
jetson-inference/python/training/classification/data（存储分类训练数据集）
jetson-inference/python/training/classification/models（存储PyTorch训练后的分类模型）
jetson-inference/python/training/detection/ssd/data（存储目标检测训练数据集）
jetson-inference/python/training/detection/ssd/models（存储PyTorch训练后的检测模型）
```

同时，可以通过：

```shell
docker/run.sh --volume /my/host/path:/my/container/path
```

来指定自定义的挂载文件（使用绝对地址）。

可以使用：

```shell
docker/build.sh
```

将当前系统打包为镜像。

### 使用ImageNet模型进行图片分类

运行```Docker/run.sh```，有了Docker的容器环境后，我们要使用的第一个深度学习应用是图像识别，使用的是在大型数据集上预训练好的分类模型。

imageNet模型在ImageNet ILSVRC数据集的1000类样本上进行训练，环境中imagenet.py就是我们要用到的程序（还有c++版的imagenet.cpp），这个模型可以识别图片、录像和摄像头的视屏流。

#### 在Jetson上使用ImageNet程序

在我们的Docker容器中，进入指定的目录：

```shell
cd jetson-inference/build/aarch64/bin
```

然后运行：

```shell
./imagenet.py images/orange_0.jpg images/test/output_0.jpg 
```

就可以实现对images文件夹下orange_0.jpg图片进行识别，结果输出在images/test文件夹下，命名为output_0.jpg。

我的运行结果是这样的：

![shell输出](/images/20201221/shelloutput.png)

![图片识别结果](/images/20201221/output_0.jpg)

程序前面运行了一段时间看起来像是在训练，具体后面写程序的时候我再研究，可以看到识别一张图片CPU用了5.39ms，GPU用了5.15ms。也可以将自己的图片放进去识别看看。

此外，可以通过指定参数使用别的预训练网络。

下一篇文章我将介绍如何在视屏中使用imagenet模型。

### 参考文献

[1]https://github.com/dusty-nv/jetson-inference/blob/master/docs/aux-docker.md

[2]https://github.com/dusty-nv/jetson-inference/blob/master/docs/imagenet-console-2.md
___




<p align = "right">2020年12月19日</p>
<p align = "right">Hang</p>

