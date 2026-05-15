# ClickHouse 组件介绍

## 概述

ClickHouse 是由 Yandex 开源的列式 OLAP 数据库管理系统，专为实时分析大规模数据而设计。它以极高的查询速度著称，能在数十亿行数据上实现亚秒级响应。

## 核心特性

- **列式存储与高压缩率**：同列数据连续存储，压缩效率极高，减少磁盘 IO
- **向量化查询执行**：利用 CPU SIMD 指令并行处理数据，显著提升查询效率
- **MergeTree 表引擎家族**：支持 ORDER BY、分区、副本等多种存储策略
- **分布式查询**：Shard + Replica 架构，支持跨节点数据合并
- **丰富的接口**：TCP Native 协议、HTTP 接口、JDBC/ODBC 驱动

## 系统架构

- **Shard + Replica 结构**：数据按分片水平切分，每个分片可有多个副本保证高可用
- **ZooKeeper**：用于 ReplicatedMergeTree 表引擎的元数据协调和副本同步
- **Distributed 表引擎**：作为查询的入口，将请求分发到各个分片并汇总结果

## 适用场景

- 日志分析与监控告警
- 用户行为分析、漏斗分析
- 实时报表与 BI Dashboard
- IoT 时序数据分析
