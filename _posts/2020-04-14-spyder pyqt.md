---
layout: post
title:  "[python]使用spyder + pyqt5开发UI"
date:   2020-04-14 20:21:00 +0800--
categories: [实践探索]
tags:   [经验记录, UI开发]
---

我的windows装python环境喜欢用anoconda，是之前在windows装tensorflow的时候觉得anoconda比较方便的缘故。

这阵子要快速做一个ui出来，看到anoconda里面有pyqt5和qt designer就打算用这一套。

spyder是anoconda预装的ide，我对ide也不挑，有用的就用，也不想装新的了。

因为spyder没办法像pycharm那样在界面上配置uic用户界面转换，所以需要执行以下几步：

1.用qt designer做个.ui文件，这个都可以做。

2.把.ui转换成.py，这个是pyqt内置的，也可以在命令行完成:

```shell
pyuic5    xx.ui -o xx. py
```

3.把ui需要用到的外部资源，比如图片之类的转换成. py

这个和uic类似，也可以用命令行完成:

```shell
pyrcc5   xx.qrc   -o  xx. py
```

通过3转换的py文件在2的最后有import，注意名称一致。

在spyder里面新建一个py文件import使用.ui生成的py文件，再使用类似如下的代码就可以进行UI开发了：

```python
import sys
from PyQt5.QtWidgets import QApplication, QDialog #要import对应的正确类型
from XX import XX  #步骤2转化的py文件和里面窗口的类

app = QApplication(sys.argv)
window = QDialog()
ui = Ui_LogInForm()
ui.setupUi(window)

window.show()
sys.exit(app.exec_())
```  
 
___




<p align = "right">2020年4月14日</p>
<p align = "right">Hang</p>

