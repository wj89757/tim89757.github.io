---
layout:     post
title:      "dubbo服务注册原理解析"
subtitle:   "dubbo服务注册与发现、服务调用过程"
date:       2018-10-22 09:20:00
author:     "Tim"
header-img: "img/dubbo.png"
tags:
    - dubbo
    - 源码阅读
---

### 简介

![](https://www.jianshu.com/p/1ff25f65587c)
![](https://www.cnblogs.com/linlinismine/p/7814521.html)


![Architecture](http://dubbo.apache.org/img/architecture.png)

### 调用关系说明

    1. 服务容器负责启动,加载,运行服务提供者。
    2. 服务提供者在启动时,向注册中心注册自己提供的服务。
    3. 服务消费者在启动时,向注册中心订阅自己所需的服务。
    4. 注册中心返回服务提供者地址列表给消费者,如果有变更,注册中心将基于长连接推送变更数据给消费者。
    5. 服务消费者,从提供者地址列表中,基于软负载均衡算法,选一台提供者进行调用,如果调用失败,再选另一台调用。服务消费者和提供者,在内存中累计调用次数和调用时间,定时每分钟发送一次统计数据到监控中心。

### 主要流程

> 1. 服务注册

```

```


> 2. 服务暴露

> 3. 服务发现

> 4. 服务调用过程。