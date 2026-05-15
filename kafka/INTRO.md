# Apache Kafka 组件介绍

## 概述

Apache Kafka 是一个开源的分布式事件流处理平台，最初由 LinkedIn 开发。它被广泛用于构建高性能数据管道、流分析、数据集成和关键任务应用，支持每秒百万级消息的发布与订阅。

## 核心特性

- **极高的吞吐量和低延迟**：基于磁盘顺序写入和零拷贝机制
- **分布式和分区架构**：Topic 可分为多个 Partition，分布在多个 Broker 上
- **数据持久化**：消息持久化到磁盘，支持可配置的保留策略
- **消费者组**：支持负载均衡消费和消费进度管理
- **KRaft 模式**：新版本可不依赖 ZooKeeper，使用内置共识协议
- **生态丰富**：Kafka Connect（数据集成）、Kafka Streams（流处理库）

## 系统架构

- **Broker**：Kafka 服务节点，负责消息存储和转发
- **Topic**：消息的逻辑分类
- **Partition**：Topic 的物理分片，是并行度和顺序性的基本单位
- **Producer**：消息发布者
- **Consumer / Consumer Group**：消息消费者和消费组
