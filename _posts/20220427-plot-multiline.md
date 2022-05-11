---
title: Plot 画折线图的各种方法
date: 2022-04-27 21:47:39
keywords: plot, matplotlib
description: Python 中 matplotlib 画折线图的方法
---

折线图是我们平时数据分析过程中最常用的，通过本次参加国际 AIOPS 比赛，积累了不少画图的经验，再此整理出来与大家分享。

> 本文使用代码在 macOS Monterey 12.2.1 测试通过，Pyhon 版本 3.9.6
>
> 本文所使用的代码和数据集可以在我的 Github 上获取

首先介绍下我使用的数据结构，这是一份时序数据，时间戳是一个乱序的一天内的时间戳，配置项ID类似于主机名，有多个重复的配置项。指标名也有多个重复的指标，最后一列是具体的值。

| timestamp  | cmdb_id  | kpi_name         | value  |
| ---------- | -------- | ---------------- | ------ |
| 时间戳     | 配置项ID | 指标名           | 指标值 |
| 1647792000 | node-1   | ping.can_connect | 1.0    |
| 1647792000 | node-2   | system.load.5    | 3.96   |

* 单折线图
* 多折线图
* 折线图标注数据点
* 折线图叠加矩形

### 单折线图

只画出一个配置项、一个指标的时间序列的折线图。

```python
# 画单折线图
def single_line():
    df = pd.read_csv('data.csv')
    # 只取一个 cmdb_id 值
    df = df[ df['cmdb_id'].str.contains('node-1')]
    # 只取一个 kpi_name 值
    df = df[ df['kpi_name'].str.contains('system.load.5') ]
    df.sort_values('timestamp')
    
    # df['value'].plot(x=df['timestamp'])
    # plt.show()

    fig = plt.figure(figsize=(14,8))
    plt.rcParams["figure.autolayout"] = True
    plt.rcParams['font.sans-serif'] = ['Songti SC']
    plt.rcParams['axes.unicode_minus'] = False

    plt.plot(df['timestamp'], df['value'], c = ‘lime’, label='node-1')
    plt.xlabel( 'Timestamp' )
    plt.ylabel( df['kpi_name'].iloc[1] )
    plt.legend( loc = 'best' )
    plt.show()
```

## 多折线图

数据文件 cmdb_id 中有两个不重复的配置项，画出这两个配置项 system.load.5 这个指标的折线图。

```python
# 画多折线图
def multi_line():
    df = pd.read_csv('data.csv')
    
    # df['value'].plot(x=df['timestamp'])
    # plt.show()
 
    fig = plt.figure(figsize=(14,8))
    plt.rcParams["figure.autolayout"] = True
    plt.rcParams['font.sans-serif'] = ['Songti SC']
    plt.rcParams['axes.unicode_minus'] = False

    # 只取一个 cmdb_id 值
    adf = df[ df['cmdb_id'].str.contains('node-1')]
    # 只取一个 kpi_name 值
    adf = adf[ df['kpi_name'].str.contains('system.load.5') ]
    adf.sort_values('timestamp')
    plt.plot(adf['timestamp'], adf['value'], c = 'teal', label='node-1')

    # 只取一个 cmdb_id 值
    bdf = df[ df['cmdb_id'].str.contains('node-2')]
    # 只取一个 kpi_name 值
    bdf = bdf[ df['kpi_name'].str.contains('system.load.5') ]
    bdf.sort_values('timestamp')
    plt.plot( bdf['timestamp'], bdf['value'], c = 'red', label='node-2')

    plt.xlabel( 'Timestamp' )
    plt.ylabel( df['kpi_name'].iloc[1] )
    plt.legend( loc = 'best' )
    plt.show()
```

### 折线图标注数据点

将故障点标注在曲线的对应位置。

```python
# 折线图标注数据点
def line_with_dot():
    df = pd.read_csv('data.csv')
    # 只取一个 cmdb_id 值
    df = df[ df['cmdb_id'].str.contains('node-1')]
    # 只取一个 kpi_name 值
    df = df[ df['kpi_name'].str.contains('system.load.5') ]
    df.sort_values('timestamp')
    
    # df['value'].plot(x=df['timestamp'])
    # plt.show()
 
    fig = plt.figure(figsize=(14,8))
    plt.rcParams["figure.autolayout"] = True
    plt.rcParams['font.sans-serif'] = ['Songti SC']
    plt.rcParams['axes.unicode_minus'] = False

    plt.plot(df['timestamp'], df['value'], c = 'red', label='node-1')

    # 故障点
    plt.plot(1647823965, df['value'].max(), 'o')
    plt.text(1647823965, df['value'].max() + 0.4, 'node-1,node节点CPU故障' , ha = 'left', va = 'top', fontsize = 8)

    plt.xlabel( 'Timestamp' )
    plt.ylabel( df['kpi_name'].iloc[1] )
    plt.legend( loc = 'best' )
    plt.show()    
```

### 折线图叠加矩形区域

在做异常检测的时候，会标注某个时间段的数据属于异常数据，这时会叠加一个矩形区域来表示。

```python
# 折线图叠加矩形区域
def line_with_rect():
    df = pd.read_csv('data.csv')
    # 只取一个 cmdb_id 值
    df = df[ df['cmdb_id'].str.contains('node-1')]
    # 只取一个 kpi_name 值
    df = df[ df['kpi_name'].str.contains('system.load.5') ]
    df.sort_values('timestamp')
    
    # df['value'].plot(x=df['timestamp'])
    # plt.show()
 
    fig, ax = plt.subplots()
    plt.rcParams["figure.autolayout"] = True
    plt.rcParams['font.sans-serif'] = ['Songti SC']
    plt.rcParams['axes.unicode_minus'] = False

    ax.plot(df['timestamp'], df['value'], c = 'blue', label='node-1')

    # 故障点
    ax.plot(1647823965, df['value'].max(), 'o')
    ax.text(1647823965, df['value'].max() + 0.4, 'node-1,node节点CPU故障' , ha = 'left', va = 'top', fontsize = 8)

    # 矩形区域
    ax.add_patch( patches.Rectangle( (1647823965, 0), 1200, df['value'].max() , facecolor = "red", alpha=0.5) )
    ax.annotate( '故障区域', xy=( 1647823965, df['value'].max()), xytext=(1647823965 + 1200, df['value'].max() - 2) )

    plt.xlabel( 'Timestamp' )
    plt.ylabel( df['kpi_name'].iloc[1] )
    plt.legend( loc = 'best' )
    plt.show()
```

这几个都是在本次参加 AIOPS 比赛过程中，为了方便查看指标情况积累的作图技巧，后续如果有折线图更多的内容，会继续在这里更新。
