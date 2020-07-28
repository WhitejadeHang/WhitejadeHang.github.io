---
layout: post
title:  "[homeassistant]homeassistant前端开发文档学习笔记"
date:   2020-04-13 15:31:00 +0800--
categories: [理论研习]
tags:   [homeassistant]
---

![homeassistant前端开发文档](/images/20200413/homeassistantfrontend.png)

### 前端构架

ha的前端构架可以分为四个部分:

**bootstrap**

在*src/entrypoints/core.js*,
是最先加载的小脚本，会做认证，以及建立和后端的websocket连接
通过这个脚本加载后端的数据同时也加载UI的其他部分。

**app shell**

在*src/entrypoints/app.js*
用于加载侧边栏和routing。

**panels**

在*src/panels/*
ha的页面都是panel，后端的组件通过注册panel在前端显示，概览/地图/日志/历史都是panel。

**more info dialog**

在*src/dialogs/more-info*
详细信息对话框，可以由组件通过dom事件触发。

#### 数据流

使用websocket api和rest api和后端通信
通过hass实例获取数据，实例里有所有应用状态，可以调用api
后端的所有变化都会更新hass。

#### routing

去中心化，每个组件只负责自己那部分功能。

#### 打包

用webpack打包应用，将整个代码拆分成很多部分。


### 前端开发

不要在生产环境中使用开发模式（效率考虑）

环境部署

1.fork代码到自己的个人仓库

2.配置前端开发者模式:

在configuration.yaml里面把前端路径改到clone的本地仓库
frontend:

Example absolute path: /home/paulus/dev/hass/frontend

development_repo: /path/to/hass/frontend/

3.安装Node.js

```shell
nvm install
```

还要安装依赖

```shell
nvm use
script/bootstrap
```

#### 开发

要动态加载

```shell
nvm use
script/develop
```

要关掉浏览器的cache

1.Network > Disable cache

2.Application > Service Workers > Bypass for network

#### 打包

script/build_frontend

#### 测试

```shell
pip3 install -e /path/to/hass/frontend/

hass --skip-pip
```

#### 前端数据

前端使用hass对象，包含最新的状态
是否状态改变：

```javascript
const changed = newVal !== oldVal;
```

可以通过选中elements，在console中输入:

```javascript
$0._hass
```

查看hass对象的属性。

#### 数据

```javascript
hass.states
```
包含所有实体的所有当前状态。

#### methods

```javascript
hass.callService(domain, service, data)

hass.callWS(message)
```

#### 外部认证

如果想将ha前端嵌入到外部app，将认证存到app中
ha有支持外部认证的api
连接要加?external_auth=1。

#### 外部总线

可以和外部app使用消息总线交互。

#### 前端扩展

添加状态卡片
ha的主要接口是当前实体和对应状态的list
每个实体可以渲染一个状态卡
sensor在没有分组的时候会使用badges的形式在状态卡的顶端

不同的badges在
/src/components/entity/ha-state-label-badge.js

添加自定义卡片方法:

1.添加“卡片名”到文件/common/const.ts的DOMAINS_WITH_CARD数组

2.在文件夹 /state-summary/创建文件state-card-文件名.js

3.添加import './state-card-文件名.js'到 state-card-content.js

添加详细信息对话框:

1、添加 '文件名' 到文件/common/const.ts的数组DOMAINS_WITH_MORE_INFO 中.

2、在文件夹 /dialogs/more-info/controls中创建more-info-文件名.js 文件.

3、添加import './more-info-camera.js'到 /dialogs/more-info/controls/more-info-content.js

#### 扩展websocket API

这部分后续编写app的时候再详细看。

### 自定义UI

**使用lovelace自定义ha的用户界面:**

可以使用除了react以外的其他前端框架，例如polymer, angular, preact等。

**自己创建卡片：**

在config目录下www文件夹创建.js的文件
可以在浏览器通过/local/目录访问
在配置文件添加
module: /local/xx.js

后面会专门写一篇关于lavelace的博客。

### 自定义panels

可以用panel_custom组件，开发文档介绍的比较少，同样也要结合后面的实践专门再写一篇panels的博客。

### 参考文献

1.[Home Assistant Frontend](https://developers.home-assistant.io/docs/frontend/)
  
___


<p align = "right">2020年4月13日</p>
<p align = "right">Hang</p>

