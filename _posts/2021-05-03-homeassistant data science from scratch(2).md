---
layout: post
title:  "[homeassistant]从0开始的homeassistant数据科学（2）-- hassdatadetective改进"
date:   2021-05-03 15:47:00 +0800--
categories: [实践探索]
tags:   [homeassistant,data science]
---

### 前言

前一期完成了环境的搭建，本期我们将重点放到连接ha后台与我们python+jupyterlab+tensorflow环境之间的关键库函数上。上期我们也介绍到了，在ha的官方网站下方有个datascience栏，其推荐的数据科学包即是hassdatadetective。这为我所设想的ha数据科学提供了方便，但还并不能完全满足我的需求，所以需要对其进行改进。

### hassdatadetective现有功能

hassdatadetective的源代码非常简单，核心功能集中在其core.py中，主要是通过sqlalchemy库中的create_engine实现对ha后台数据库的操作，各功能主要封装在core.py中HassDatabase类中，因为其非常简单，我们可以一个个详细看看。

```python
__init__(self, url, *, fetch_entities=True)
```

类的构造函数，主要干的事情就是在创建HassDatabase类的时候通过`url`参数连接到数据库，默认还会获取ha数据库的所有实体，并且保存数据库类型。

```python
perform_query(self, query, **params)
```

这是基础的sql查询命令封装，从连接的数据库执行相应的查询语句，失败会报错。

```python
fetch_entities(self)
```

这条语句按照ha里面的entity_id来查询实体，然后将结果保存在HassDatabase对象的entities属性中。

```python
fetch_all_sensor_data(self, limit=50000) -> pd.DataFrame
```

这个函数获取所有的传感器数据，默认最多50000条，指定参数`limit=None`则可以获取所有数据，返回值指定为pd.DataFrame。

函数的查询语句是从states表中查询domain是binary_sonsor和sensor而且状态不是unknown和unavailable的内容。

这里函数没有用到前面连接的数据库引擎，而是使用pandas读取数据库的工具`pd.read_sql_query`直接将查询结果保存为了dataframe格式。

```python
fetch_all_data_of(self, sensors: Tuple[str], limit=50000) -> pd.DataFrame
```

这是目前hassdatadetective的核心功能，获取指定传感器的数据，还是最多50000条，方法和上个函数类似，同样是过滤了状态unknown和unavailable的内容。



#### states数据表

states数据表包含的数据项有:

> state_id: 表示状态的编号，是从1顺序增长的。

> domain: 表示状态所属的类型，包括person、sun、zone、sensor、device_tracker等等。

> ×entity_id×: 实体id，可对应状态所属的唯一实体，是我们查询数据的关键。

> state: 状态，是我们要查询的内容，可以是数值，也可以是文字描述。

> attributes: 对state的详细描述，包括简单说明，单位，别名，显示的图标等。

> envent_id: 状态变更事件所对应的事件id。

> last_changed: 上次状态变更时间时间。

> last_updated: 上次状态更新时间。

> created: 状态创建时间。

> old_state_id: 上个状态的id。

#### events数据表

events数据表包含的数据项有：

> event_id: 表示事件的编号，从1顺序增长。

> event_type： 事件类型，包含service_registered、componet_loaded、panels_updated、state_changed、call_service、platform_discovered等。

> event_data：事件触发数据描述，类似于states表的attributes。

> origin：事件触发来源，包括local和remote，目前只有app触发事件是remote。

> time_fired：事件触发时间。

> created：事件创建时间。

> context_id：是一串哈希码，暂时还不知道作用。

> context_user_id：事件对应的用户的哈希。

> context_parent_id：目前都是NULL。

### hassdatadetective改进目标

1）按照需求实现功能

2）进行数据完整性、合规性验证

3）数据缺失补齐与异常处理

4）数据频率对齐、重采样

### hassdatadetective改进步骤规划

1）编写sql语句实现需求1）、2）

2) 利用hassdatadetective框架，完成python接口函数将sql结果转换为dataframe

3) 利用dataframe功能完成剩余改进目标

4）编成新的包

### 参考文献

1.[Recorder](https://www.home-assistant.io/integrations/recorder)


  
 
___




<p align = "right">2021年4月5日</p>
<p align = "right">Hang</p>

