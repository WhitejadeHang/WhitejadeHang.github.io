---
layout: post
title:  "[homeassistant]homeassistant Utility Meter"
date:   2020-07-27 16:05:00 +0800--
categories: [实践探索]
tags:   [homeassistant]
---

### Utility Meter介绍

Utility Meter是HomeAssistant的一个组件，可以记录各种表记比如电表，水表，气表等等的消耗。

是为用户提供消费查询用的，所以是按时间来循环的，比如每月/每周清零。

有些表的计费规则会随时间或消费变化，这些也可以自定义，需要调用HomeAssistant的相关服务。

### 配置

在HomeAssistant的configuration.yaml里进行配置：

'''yaml
utility_meter:
  energy:
    source: sensor.energy_in_kwh
    cycle: monthly
'''

其中source是一个sensor，cycle可以是小时，日，周，月，季度，年，这两个是必填项。

还可设置offset，每个周期从起始点偏移多久，还可以设置成双向表，可正可负，还可设置费用。

这里的source需要是电量等，如果像我采集的是功率，可以用Home Assistant的[integraion](https://www.home-assistant.io/integrations/integration/)组件进行计算转换为电量，我将其转换为了kWh。

'''yaml
sensor:
  - platform: integration
    source: sensor.current_power
    name: energy_spent
    unit_prefix: k
    round: 2
'''

### 服务

包含清空/校准/进入下一级计费/选择计费，注意这里的计费是一个名字，相当于一个标记，读的数还是一样的，也就是说只是按一个条件给做了划分，要实现峰谷电价或阶梯电价除了要设置条件外，计算的sensor也要各自配。

### 高级配置


### 参考文献

1.[homeassistant integrations Utility Meter](https://www.home-assistant.io/integrations/utility_meter/)
2.[homeassistant integrations Integration - Riemann sum integral](https://www.home-assistant.io/integrations/integration/)

  
 
___




<p align = "right">2020年7月27日</p>
<p align = "right">Hang</p>

