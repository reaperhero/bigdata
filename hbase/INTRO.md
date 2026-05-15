# Apache HBase 组件介绍

## 概述

Apache HBase 是建立在 HDFS 之上的分布式、面向列的 NoSQL 数据库，源自 Google 的 Bigtable 论文。它适用于需要随机、实时读写海量数据（数十亿行 × 数百万列）的场景。

## 核心特性

- **海量数据存储**：线性扩展，支持 PB 级数据
- **强一致性读写**：同一行数据的读写保证原子性
- **自动分片**：表按 Row Key 范围自动切分为 Region，分布到各 RegionServer
- **与 Hadoop 生态深度集成**：使用 HDFS 作为底层存储，支持 MapReduce 批量处理
- **列族存储**：数据按列族组织，不同列族可有不同的存储策略

## 系统架构

- **HMaster**：负责 Region 分配、DDL 操作、负载均衡
- **RegionServer**：管理 Region，处理读写请求
- **ZooKeeper**：协调 HMaster 选举、存储集群元数据
- **HDFS**：底层数据持久化存储
