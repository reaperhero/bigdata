# DataX 组件介绍

## 概述

DataX 是阿里巴巴开源的离线数据同步工具，实现各种异构数据源之间高效的数据同步。它采用 Framework + Plugin 架构，通过 Reader 和 Writer 插件支持数十种数据源，包括 MySQL、Oracle、PostgreSQL、HDFS、Hive、HBase、Elasticsearch 等。

## 核心特性

- **异构数据源支持**：内置 50+ 种 Reader/Writer 插件，覆盖关系型数据库、NoSQL、大数据存储、云存储等
- **Framework + Plugin 架构**：框架负责调度、缓冲、流控，插件负责具体的数据读写
- **高吞吐并发**：将一个 Job 拆分为多个 Task，通过 TaskGroup 并行执行
- **脏数据处理**：完善的脏数据记录和限制机制
- **运行日志**：详细的同步速率、成功/失败记录数统计

## 核心架构

```
Reader → Channel（缓冲队列）→ Writer
         ↑ Framework 控制流速、并发、转换
```

- **Job**：一次数据同步的最小单元，由 JSON 配置文件定义
- **Task**：Job 按数据切分后的子任务
- **TaskGroup**：一组 Task 的运行容器，控制并发度
- **Channel**：Task 间的数据传输通道，带缓冲和流控
