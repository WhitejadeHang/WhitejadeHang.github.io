---
layout: post
title:  "[homeassistant]小米米家台灯接入homeassistant使用siri控制"
date:   2020-04-18 20:56:00 +0800--
categories: [实践探索]
tags:   [homeassistant, 经验记录]
---

偶然获得了一台小米台灯，发现可以联网，于是准备拿来接入到homeassistant里，用siri控制，家里16年的ipad拿来当当音箱吧。

## 米家台灯接入

小米米家台灯属于yeelight系列
米家台灯只能通过手动配置的方式加入:

```
# Example configuration.yaml entry
discovery:
  ignore:
    - yeelight
yeelight:
  devices:
    192.168.1.25:
      name: Living Room
```

这里遇到一个问题是我开始用米家app连接设置灯泡，查找ip地址，看到是局域网控制模式，但配置完后homeassistant识别到灯却不能控制。

之后又下载了yeelight app开启局域网控制模式，就可以了。
 
除了开关，米家台灯可用到的服务是


> SERVICE YEELIGHT.SET_COLOR_TEMP_SCENE

可以调节亮度和色温，色温的范围好像是1700k-6500k

## homekit接入

homekit组件是用homekit控制homeassistant

homekit controller是用homeassistant控制homekit

在homeassistant的配置里添加：

```
homekit:
```

打开homeassistant的前端查看pin code，然后在苹果的home app里面添加配件，通过pin code的方式，就可以把homeassistant添加为homekit的配件了。

这里我用了include参数，只添加了这个台灯

其实更有用的应该是添加automation，homekit能识别automation实体。

所有在homekit识别到的配件都可以用siri控制，当然，其实这个也不是很方便。

在刚弄好的时候体验了一下，很快就兴趣消退了，siri现在还不如小爱同学&#128514;。

这块还只是个基本的开头，后面能做的工作还很多&#128170;。
___

### 参考文献

1.[homeassistant integrations Yeelight](https://www.home-assistant.io/integrations/yeelight/)
2.[homeassistant integrations HomeKit](https://www.home-assistant.io/integrations/homekit/)


<p align = "right">2020年4月18日</p>
<p align = "right">Hang</p>

