# Apache Kudu 组件介绍

## 概述

Apache Kudu 是为 Hadoop 平台开发的列式存储管理器，兼顾低延迟随机读写与高速分析查询。它填补了 HDFS（高吞吐顺序读写）和 HBase（低延迟随机读写）之间的空白，常与 Impala、Spark 配合使用。

## 核心特性

- **快速行级操作**：支持快速的 INSERT、UPDATE、DELETE
- **列式存储**：针对分析查询优化，支持列裁剪和压缩
- **与 Impala/Spark 深度集成**：可直接通过 SQL 操作 Kudu 表
- **基于 Raft 的高可用**：Master 和 Tablet 副本通过 Raft 协议保证一致性
- **灵活的分区策略**：支持哈希分区、范围分区及组合分区

## 系统架构

- **Kudu Master**：负责元数据管理、表定义、Tablet 分配
- **Tablet Server（TServer）**：实际存储数据分片（Tablet）
- **Tablet**：表数据的物理分片，每个 Tablet 有多个副本
