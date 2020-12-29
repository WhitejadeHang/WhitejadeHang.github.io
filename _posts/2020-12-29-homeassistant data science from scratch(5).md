---
layout: post
title:  "[homeassistant]从0开始的homeassistant数据科学（5）--使用DetectNet实现目标检测"
date:   2020-12-29 22:44:00 +0800--
categories: [实践探索]
tags:   [homeassistant]
---

前面我们使用了imagenet模型识别一整张图片，接下来我们要用detectnet进行图像内的多目标检测。我们的输入是一张图，输出是图上的坐标边界（形成一个框），以及对应的物体类型和可信度。

程序默认使用的是在MS COCO数据集上训练的SSD-Mobilenet-v2来检测的，这个模型可以在Jetson系列上实现实时解析。

### 检测图片中的目标

MS COCO数据集包含了人、车、动物、家庭常见物品。我们用的detectnet包含输入、输出参数和其他四个可选参数：

* --network

指定目标检测模型，可用的模型可参考[此列表](https://github.com/dusty-nv/jetson-inference/blob/master/docs/detectnet-console-2.md#pre-trained-detection-models-available)。

* --overlay

输出表现控制，可以包括box、labels、conf或者是none，默认包含前三个，也就是会显示一个检测出物体范围的框，包含其标签和识别可信度。

* --alpha

是用于控制overlay的透明度，最高254是不透明，默认120。

* --threshold

是结果显示出来的准确度阈值，默认是0.5，也就是说检测结果有50%可信度就会在结果上显示出来。

接下来，使用：

```shell
./detectnet.py images/peds_1.jpg images/test/output.jpg
```

这样的命令即可实现图片目标检测并将结果输出到文件夹内。我测了两张，第一张由于要将模型加载到内存，前期准备时间比较久，实际识别时间是50多毫秒，第二张图片由于模型已经存在于GPU了，直接是70毫秒左右出结果，效果如图：

![检测结果](/images/20201229/detectres.png)

### 检测视频流中的目标

与我们做图像识别时类似，我这里还是用安卓手机的ip摄像头来做视屏流目标检测，调用方式和之前一致：

```shell
./detectnet.py --input-codec=h264 rtsp://your.ip.address.and:port/h264_pcm.sdp
```

![视屏结果](/images/20201229/videores.png)



___




<p align = "right">2020年12月23日</p>
<p align = "right">Hang</p>

