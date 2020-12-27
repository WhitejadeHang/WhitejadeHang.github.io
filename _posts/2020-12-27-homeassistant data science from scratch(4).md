---
layout: post
title:  "[homeassistant]从0开始的homeassistant数据科学（4）--2020年末关于数据科学的一些规划"
date:   2020-12-27 19:40:00 +0800--
categories: [实践探索]
tags:   [homeassistant]
---

这个月开始，我的数据科学相关工作算是一点点走上轨道：硬件资源上，有了稳定的开发设备；时间分配上，后续希望能更多的投入。希望能将数据科学持续的进行下去，提升认知也好、方便生活也好、促进工作也好，这项技术可以发挥的地方很多。所以在一点点做技术积累的期间，时不时的，我也做一下规划，以保证自己至少沿着自认为正确的方向前进。

### 框架选择部分

框架方面，基本的就用python系列，numpy\pandas\scipy\sklearn等等自不用说，主要考虑深度学习框架tensorflow和pytorch。我开始接触深度学习的时候使用的是tensorflow，目前它仍是最流行的开源深度学习框架，它的问题在于1.x版本和2.x版本基本等于两个框架，1.x更偏底层，需要单独设置的地方比较多，2.x在使用上有点像pytorch了。此外在它之上还有可以快速构建模型的keras库。

pytorch我没有用过，看介绍，使用方式比较贴近python本身的模式，入门应该也不难，最近上升趋势很快，用在研究上比产业上的多。而且在移动端或者web端要部署的话，pytorch缺少相关支持，可能还是得用tensorflow。

最近新开源的框架也比较多，我呢怎么说也还算初学，需要多看教程，复现别人的模型之类的，所以先不考虑那些。对于模型部分，我还是先学tensorflow2.x，目前考虑的是英国帝国理工大学的[Getting started with TensorFlow 2](https://www.coursera.org/learn/getting-started-with-tensor-flow2/home/welcome)，预计2周内学完。然后把斯坦福的[CS20](https://web.stanford.edu/class/cs20si/syllabus.html)补一下，这个是tensorflow1.X的教程，我去看看能不能把代码改得在2.x上跑，这个看看可行性再预估学习时间，之后就是找个keras的教程学习。

此外就是多读论文、上kaggle等等，复现别人的模型，在遇到必须要Pytorch的地方(或者其他框架的地方)，再进行系统、快速的框架学习。总的来说，就是以tensorflow2.x作为主要工具，不要排斥其他框架。

### 环境搭建部分

依照框架选择的思路，那么环境选择上我就在xavier nx的系统上安装tensorflow2.x，安装方法参考[官方教程](https://docs.nvidia.com/deeplearning/frameworks/install-tf-jetson-platform/index.html)，写得很详细我就不多说了，只提一点就是在做好预先准备后，由于最后是在英伟达的网站去下载的资源，这个网站现在国内非常不稳定，我在命令行下载是失败的，在做好代理后，直接在网页上下载文件，然后用wheel安装，可以查看[手动安装教程](https://blog.csdn.net/wc996789331/article/details/90814105)。

这以外的环境，一部分是英伟达所提供的包括opencv等后面我都做成docker镜像，以容器的方式来配置，例如我前面正在学习的jetson-inference系列教程就是用的容器。

### 算法学习部分

算法部分一方面跟着教程复习一下简单的回归、卷积神经网络、LSTM等等，现在也有摄像头了，基本什么例子都可以跑，多实践，然后就是论文和数据大赛里的算法，最前沿的像是gan\yolo\transformer等等，多拿来跑跑看。

由于目前主流的深度学习都是在做图像视频之类的，根据我的需求，时间序列、波形分析、市场仿真方面的算法要多留意。

### 配套学习部分

为了支持深度学习开发，必要时需要补充数学方面的知识，这个我已经下载好了教程，需要时去看。

然后，容器已经开始在学习了，包括容器集群的部署，近期内快速掌握。

然后有个python数据可视化的教程我很喜欢，可以顺便复习一下python中的一些细节，做好可视化对成果宣传、论文都很有好处。

DSP，是我做波形等数据分析的好用的技术，一直都想好好学的，这个看时间安排。

数据库，目前我用MySQL，有一本书一月看完，还有之前的大量家庭数据是SQLite存储的，要学会用Python高效调用，最好能把MySQL和SQLite整合到一起。

电力市场仿真，有几个Python开源的库，要学习一下使用，同时涉及到电力系统的部分也要补一下。

Multiagent，这个也是在市场仿真的时候要涉及的，日内瓦大学有个[Simulation and modeling of natural processes](https://www.coursera.org/learn/modeling-simulation-natural-processes?page=7&index=prod_all_products_term_optimization)后面务必要去学。

### 数据准备部分

目前我手上有的有40G左右的homeassistant家庭数据，想用Python访问SQLite先提取出来分析着。

另外有约1G的电力数据，想在1月初步完成负荷预测，后续再研究深化应用。

还有下载了1G多的美国电力市场与新冠数据，读了论文我也可以跑跑相应模型。

还有kaggles上的[家庭数据集](https://www.kaggle.com/uciml/electric-power-consumption-data-set)也可以使用。


___




<p align = "right">2020年12月27日</p>
<p align = "right">Hang</p>

