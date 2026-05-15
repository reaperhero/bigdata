# Apache YARN 组件介绍

## 概述

Apache YARN（Yet Another Resource Negotiator）是 Hadoop 2.x 引入的集群资源管理系统。它将资源管理与作业调度解耦，使得多种计算引擎（MapReduce、Spark、Flink、Tez 等）可以共享同一个集群的资源。

## 核心特性

- **统一资源管理**：CPU 和内存资源集中调度
- **多引擎共享**：MapReduce、Spark、Flink 等共用集群
- **高可用**：ResourceManager 支持 Active/Standby HA
- **灵活调度**：支持 Capacity Scheduler 和 Fair Scheduler

## 系统架构

| 组件 | 角色 |
|------|------|
| ResourceManager | 全局主节点，负责资源仲裁和应用调度 |
| NodeManager | 各节点上的代理，管理容器（Container）资源 |
| ApplicationMaster | 每个应用的管理进程，向 RM 申请资源并协调 Task |
| Container | 资源分配的基本单位（CPU + 内存）|
