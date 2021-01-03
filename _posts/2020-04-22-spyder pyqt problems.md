---
layout: post
title:  "[python]使用spyder + pyqt5开发UI的问题整理"
date:   2020-04-22 10:58:00 +0800--
categories: [实践探索]
tags:   [经验记录, UI开发]
---

## 程序只能运行成功一次

这好像是spyder的一些问题，在程序入口添加：

```python
app.aboutToQuit.connect(app.deleteLater)
```

会有所改善，但没有完全解决。

## 界面错位

这是我的外接显示器不是标准比例的问题，多运行一会儿会好。

## 窗口控件大小自适应

pyqt自适应需要自己来设置布局，这个是要提前考虑的。

## 界面美化

说实话我很不擅长设计美化，所以这次我用了别人的库。

具体方法是import别人的窗口样式，然后实例处用样式继承我编的窗口。

## Qlabel添加图片和点击事件

我使用Qlabel+图片做快捷键，使用QPixmap来加载图片资源：

```python
self.label.setPixmap(QPixmap("file\\img"))
```
<span id=1> </span>

注意windows环境下路径要用\\，在linux环境下用/。

Qlabel没有自带的点击事件，找到最方便的方法是：

```python
self.label.mousePressEvent = self.your_func
```

然后在函数定义那里多加一个event参数:

```python
    def your_func(self, event):
    	some function
```

## tab之间跳转

使用setCurrentIndex方法完成，从0开始，注意如果有多级tab，各级要对应好。

## pyqtgraph画图

使用pyqtgraph里面的PlotWidget()，可设定背景颜色、坐标标签，网格，笔刷，图例等。要在一张图上画多条曲线就用多个plot就可以了。

## 简单的多线程

画图的时候，我使用setData来动态刷新绘图，但是用到time.sleep会让界面停住，要用的多线程。

```python
import threading

self.ui.mythread = threading.Thread(target=self.update_powercureve_data,name='update')
self.ui.mythread.start()
```

这样讲更新放到新起的线程里面，就可以同步刷新了。

## 读取excel

没装pandas，这里就用xlrd

```python
import xlrd

mybook = xlrd.open_workbook('data.xls')
self.ui.mydata = mybook.sheets()[0]
```

这样可以将excel里的第一张sheet读出来。

## 获取当前时间

```python
cur_time = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())
```

## 简单进度条

进度条实例要放到循环里去，进度数0-100%要自己设的。

所以如果循环计数不是100的话，要换算一下。

另外进度条也是会占用当前进程的，所以要不影响界面操作也需要单独给进度条开一个线程。

## 跨平台运行

我在windows环境下开发的，目标运行环境是linux。

这里主要解决依赖的问题，将所以import内容在linux目标环境上装好，并按照上述[Qlabel添加图片和点击事件](#1)修改资源路径。

需要注意的是，正确配置后，使用：

```shell
python3 xxx.py
```

没有显示出图片资源，而使用：

```shell
./xxx.py
```

则可以正常显示。

## 后续工作
  
在linux环境程序打包

触摸屏的输入验证

开机跳过启动界面和桌面，直接进入程序 
___




<p align = "right">2020年4月22日</p>
<p align = "right">Hang</p>

