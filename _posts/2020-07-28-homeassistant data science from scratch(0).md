---
layout: post
title:  "[homeassistant]从0开始的homeassistant数据科学（0）--相关组件"
date:   2020-08-03 14:06:00 +0800--
categories: [实践探索]
tags:   [homeassistant]
---

### 前言

关于智能家居，从去年6月最初想法，到8月参加创业比赛，再到11开始动手实施，想法已有1年有余，行动也有半年多了。

从开始自己做高频电信号采样到后来接触homeassistant，想法和方案也在不断完善与成熟，最终的目标还是真正的实现AIOT。

It Takes Complexicity to Achive Simplicitys.

要做的是简化而不是替代，是融通而不是分立，是智能而不是规则。

现在，我已经有足够的传感器数据了，从0开始迈出的第一步，先熟悉homeassistant相关的三个组件。

### Recorder组件

Recorder是做数据科学最关键的一个组件。

通过recorder把数据保存到数据库，在homeassistant可以用于frontend和history的显示。

对于用树莓派SD卡保存数据，这个组件的频繁读写可能会让存储介质有点吃不消，最好换别的设备来存。

我现在就是用的默认sqlit在树莓派homeassistant配置文件路径下，sqlite非常简单，相应的效率也会比较低。

后面开展数据科学工作，我需要把recorder数据库换到qnap上去，改用MariaDB。

homeassistant使用的[SQLAlchemy](https://www.sqlalchemy.org/)可以操作任何的SQL数据库。

recorder的配置参数：

** db_url ** : 数据库地址；

** db_max_retries ** : 最大重连次数，默认10次;

** db_retry_wait ** : 每次重连等待时间，默认3秒;

** auto_purge ** : 自动清除，默认开启，做数据科学最好设为false;

** purge_keep_days ** : 清除时保留记录的天数，默认为10， 如果auto_purge开启，那么这个设大点，如365也可以;

** commit_interval ** : 状态改变和事件触发的提交间隔，默认是1秒，基本满足数据科学的需求了;

** exclude ** : 
	用于排除掉不需要记录的附带数据和计算用的中间数据，减小数据库大小，数据科学工作是必要的。可详细配置。
	- domains: 要排除的域；
	- entity_globs: 要排除的模糊匹配实体；
	- entities: 要排除的具体实体；
	- envent_types: 要排除的事件类型；

** include ** :
	用于指定要记录的数据。可与exclude结合使用实现精细过滤。
	- domains: 要包含的域；
	- entity_globs: 要包含的模糊匹配实体；
	- entities: 要包含的具体实体；
	例如，在排除了某个域后，可再包含域下的具体实体。

对于具体需要排除和包含哪些数据，在完成数据库迁移后，需要整体分析现在的数据记录情况和打算开展的数据分析工作，确定哪些数据是一定不用的，哪些数据是必须包含的。

recorder还可调用recorder.purge服务，指定保留数据的天数和重新创建数据库。

注意在数据库迁移的时候，可能需要指定端口，还有就是如果使用虚拟环境，要安装好相关的依赖。

具体的对应数据库的安装注意，可查看[recorder](https://www.home-assistant.io/integrations/recorder/)文档。


### History组件

History组件是基于recorder组件的展示用组件，仅用于前端展示，对于数据科学没有作用，可以尽量少的记录自己关心的历史数据。

和recorder类似，可配置exclude和include:

** exclude ** : 
	- domains: 要排除的域；
	- entities: 要排除的具体实体；

** include ** :
	- domains: 要包含的域；
	- entities: 要包含的具体实体；


### Database组件

严格来说，Database不算是一个真正的组件，而是recorder、histroy所用到的数据库构成。

homeassistant官方也出了一个data science的[文档](https://data.home-assistant.io/)，希望在homeassistant内部进行数据分析。

对我来说呢，不太愿意在homeassistant里面用jupyterlab来写统计类的程序，而是独立的从数据库读到数据后，结合tensorflow等框架实现自己想要的功能。

homeassistant的内核是事件驱动的，数据模型包括：事件、状态、服务和上下文。这些在database里都有具体体现。

#### events

evnets是homeassistant的基础，states和services等都是基于事件的。homeassistant的内核由一些列内置的事件组织在一起。

内置事件包括：

state_changed: entity_id , old_state , new_state

automation_triggered: name , entity_id

script_started: name , entity_id

service_registered: domain , service

home_assistant_start

home_assistant_stop

所有的events存储在database的events表中。

#### states

在homeassistant中，物联网设备和服务的数据等都使用states来表示。一个实体在内存中就是一个state, 每个实体都有一个domain和唯一的entity_id。

所有的states存储在database的states表中。

#### services

homeassistant中的每个组件都可以提供services。services可以控制内部的实体或组件，也可以执行外部的服务，服务是通过service_registered事件来注册的，没有单独记录。

#### context

context是用来联系事件与状态的，每当有新的变化触发时，就会产生相应的context。

context对象包含的预：

context_id , user_id , parent_id

#### recorder runs

每次homeassistant正常启动就会产生一次新的recorder run，可以用来保证所取的状态数据的时效性和正确性。

### 参考文献

1.[homeassistant integrations Recorder](https://www.home-assistant.io/integrations/recorder/)

2.[homeassistant integrations Database](https://www.home-assistant.io/docs/backend/database/#schema)

3.[homeassistant integrations History](https://www.home-assistant.io/integrations/history/)

4.[homeassistant Data Science Portal](https://data.home-assistant.io/)
 
 
___




<p align = "right">2020年8月3日</p>
<p align = "right">Hang</p>

