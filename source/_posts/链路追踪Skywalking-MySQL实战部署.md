---
title: 链路追踪Skywalking+MySQL实战部署
date: 2023-02-03 16:51:27
tags: 运维
---

## 一、链路追踪skywalking

### 基本介绍

- 什么是链路追踪

随着微服务分布式系统变得日趋复杂，越来越多的组件开始走向分布式化，如分布式服务、分布式数据库、分布式缓存等，使得后台服务构成了一种复杂的分布式网络。在服务能力提升的同时，复杂的网络结构也使问题定位更加困难。在一个请求在经过诸多服务过程中，出现了某一个调用失败的情况，查询具体的异常由哪一个服务引起的就变得十分抓狂，问题定位和处理效率是也会非常低。

分布式链路追踪就是将一次分布式请求还原成调用链路，将一次分布式请求的调用情况集中展示，比如各个服务节点上的耗时、请求具体到达哪台机器上、每个服务节点的请求状态等等。

- 为什么要使用链路追踪

链路追踪为分布式应用的开发者提供了完整的调用链路还原、调用请求量统计、链路拓扑、应用依赖分析等工具，可以帮助开发者快速分析和诊断分布式应用架构下的性能瓶颈，提高微服务时代下的开发诊断效率。

- skywalking 链路追踪

SkyWalking是一个可观测性分析平台（Observability Analysis Platform 简称OAP）和应用性能管理系统（Application Performance Management 简称 APM）。

提供分布式链路追踪，服务网格（Service Mesh）遥测分析，度量（Metric）聚合和可视化一体化解决方案。



SkyWalking 特点

- 多语言自动探针，java，.Net Code ,Node.Js
- 多监控手段，语言探针和Service Mesh
- 轻量高效，不需要额外搭建大数据平台
- 模块化架构，UI ，存储《集群管理多种机制可选
- 支持警告
- 优秀的可视化效果。

## 二、Skywalking环境部署

### 2.1 简介

部署版本：skywalking8.5

skywalking环境主要分为三个部分：

1. skywalking-oap 核心服务
2. skywalking-ui  可视化操作界面
3. skywalking-agent 需要部署到监控的每个微服务中，起到发送监控数据到skywalking-oap中的作用

![img](skywalking-时序图.svg)

### 2.2 Docker单机部署

#### skywalking-oap部署

1. 启动容器需要修改oap-server中的配置：注册中心配置（没有可以不配置）、持久化配置。
   1. 配置文件地址：apache-skywalking-apm根目录 -> config -> application.yml
   2. 个人不建议在此文件中配置，可以使用环境变量替换配置值
2. 如果需要使用MySQL做持久化，还需要将mysql-connector-java.jar传入到skywalking-oap容器内部的/skywalking/oap-libs/目录下。
3. 制作Dockerfile

```shell
FROM apache/skywalking-oap-server:8.5.0-es7
MAINTAINER chenxin
COPY oap-libs/mysql-connector-java-5.1.47.jar /skywalking/oap-libs/mysql-connector-java-5.1.47.jar

# 【可选】告警功能配置 详细见下文告警功能配置部分
COPY alarm-settings.yml /skywalking/config/alarm-settings.yml

# 声明容器访问端口
EXPOSE 1234
EXPOSE 11800
EXPOSE 12800

# 环境变量
# 持久化使用MySQL
ENV SW_STORAGE=mysql SW_JDBC_URL="jdbc:mysql://xxx.xx.xx.xx:3306/skywalking" SW_DATA_SOURCE_USER=root SW_DATA_SOURCE_PASSWORD=123456
# 数据清理间隔日期：1天
ENV SW_CORE_METRICS_DATA_TTL=1 SW_CORE_RECORD_DATA_TTL=1
```

1. 容器启动命令

```shell
# 启动容器
docker run --name skywalking-mysql -d \
-p 1234:1234 -p 11800:11800 -p 12800:12800 --restart always skywalking-mysql
```

#### skywalking-ui部署

1. 只需要配置skywalking-oap-server的地址即可，直接使用docker run命令启动
2. 容器启动命令。 [skywalking-mysql]是skywalking-oap的容器名

```shell
docker run --name skywalking-mysql-ui -d -p 8888:8080 --restart always \
--link skywalking-mysql -e SW_OAP_ADDRESS=skywalking-mysql:12800 \
apache/skywalking-ui:8.5.0
```

## 三、项目接入部署

### 3.1 在服务中部署agent代理

#### 1. 服务部署添加agent

1. 将skywalking-agent中agent目录放入容器中，如下图的：COPY skywalking-agent/agent /agent/
2. 修改环境遍历配置，指定skywalking-oap-server的地址
3. 在Java启动命令中使用javaagent命令配置代理信息

```shell
FROM java:8
MAINTAINER chenxin
COPY java-study-1.0-SNAPSHOT.jar java-study.jar
EXPOSE 8080

# 1. 将skywalking-agent中agent目录放入容器中
COPY skywalking-agent/agent /agent/

# 开启追踪白名单插件（文件在skywalking-agent项目包内）
COPY agent/optional-plugins/apm-trace-ignore-plugin-8.5.0.jar /agent/plugins/apm-trace-ignore-plugin-8.5.0.jar
# 配置追踪白名单（见下文）
COPY apm-trace-ignore-plugin.config /agent/config/apm-trace-ignore-plugin.config

# 2. 环境变量配置 接入我们的skywalking-server
ENV SW_AGENT_COLLECTOR_BACKEND_SERVICES=xxx.xxx.xx.xx:11800 SW_GRPC_LOG_SERVER_HOST=xxx.xxx.xx.xx

# 3. 勾子配置  -javaagent:/agent/skywalking-agent.jar=agent.service_name=java-study
ENTRYPOINT ["java", "-javaagent:/agent/skywalking-agent.jar=agent.service_name=java-study","-Duser.timezone=GMT+08","-Xms128m","-Xmx128m", "-jar", "java-study.jar"]
```

#### 2. 追踪白名单配置

防止redis、注册中心等心跳检测，以及Mysql的连接请求刷出大量追踪记录

```shell
vim apm-trace-ignore-plugin.config

# 写入以下内容
trace.ignore_path=${SW_AGENT_TRACE_IGNORE_PATH:Redisson/**,Mysql/JDBI/Connection/**,Mysql/JDBI/Statement/**}
```


#### 2. 应用日志打印加入追踪id（定位日志）

具体配置见下文 3.2



## 四、可选配置

### 4.1 告警功能配置【可选】

1. 修改替换skywalking-oap-server中的config/alarm-settings.yml文件，修改其中webhooks地址(通知地址)
2. 实现通知notify接口（接口只能为POST）

### 4.2 应用日志打印加入追踪id（定位日志）

方案：修改项目日志打印格式，配置自定义参数tradeId，使每一行日志都能输出对应追踪id。

详情可以参考我的另一篇文章：关于Java配置自定义日志格式、自定义参数问题

使用示例：

![img](log_1.png)

![img](trade_1.png)