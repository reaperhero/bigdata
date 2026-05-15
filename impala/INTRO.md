# Apache Impala 组件介绍

## 概述

Apache Impala 是一个开源的大规模并行处理（MPP）SQL 查询引擎，用于对存储在 Hadoop 上的数据进行交互式分析。它跳过 MapReduce，直接访问 HDFS/HBase/Kudu 数据，实现亚秒级到数秒级的查询响应。

## 核心特性

- **交互式查询**：亚秒级到数秒级的 SQL 响应
- **无 MapReduce 开销**：直接访问底层存储，绕过批处理框架
- **兼容 Hive Metastore**：与 Hive 共享元数据，Hive 建的表 Impala 可以直接查
- **支持多种存储**：HDFS、HBase、Kudu、S3
- **标准 SQL**：兼容 HiveQL 大部分语法

## 系统架构

- **Impalad**：运行在每个 DataNode 上的守护进程，负责执行查询
- **StateStored**：跟踪集群中所有 Impalad 的健康状态
- **Catalogd**：管理元数据变更并广播到所有节点
