# Apache Flink 组件介绍

## 概述

Apache Flink 是一个开源的分布式流处理框架，用于在无界和有界数据流上进行有状态的计算。它以高吞吐、低延迟和精确一次（Exactly-once）语义著称，是构建实时数据管道和流式分析应用的首选引擎。

## 核心特性

- **流批统一**：使用同一套 API 处理流数据和批数据
- **精确一次语义**：通过 Checkpoint 机制保证状态一致性
- **事件时间处理**：支持 Event Time 和 Watermark，正确处理乱序数据
- **强大的窗口机制**：滚动窗口、滑动窗口、会话窗口、全局窗口
- **丰富的连接器**：Kafka、JDBC、Elasticsearch、HDFS、HBase 等
- **Flink SQL**：标准 SQL 接口，降低流处理开发门槛

## 系统架构

- **JobManager**：集群的主节点，负责作业调度、Checkpoint 协调、故障恢复
- **TaskManager**：工作节点，运行具体的算子（Task），管理内存和网络缓冲
- **Dispatcher**：接收作业提交请求，启动 JobManager
- **ResourceManager**：管理 TaskManager 资源（Slot 分配）
