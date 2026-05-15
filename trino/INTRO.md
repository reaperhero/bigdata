# Trino 组件介绍

## 概述

Trino（原名 PrestoSQL）是一个快速的分布式 SQL 查询引擎，由 Presto 的原始创建者从 Facebook 离职后继续开发。它能在不移动数据的前提下查询各种异构数据源，完全兼容 ANSI SQL。

## 核心特性

- **极致查询性能**：基于内存的流水线执行
- **联邦查询**：单条 SQL 可关联多个不同数据源
- **庞大的 Connector 生态**：支持 40+ 数据源（Hive、Iceberg、Delta Lake、MySQL、PostgreSQL、MongoDB 等）
- **ANSI SQL 标准**：完整的 SQL 支持
- **活跃社区**：版本迭代快，功能不断增强

## 系统架构

- **Coordinator**：接收查询、解析 SQL、生成执行计划、调度 Worker
- **Worker**：执行具体的计算任务，从数据源获取数据
- **Connector**：与外部数据源通信的插件
- **Catalog**：命名的数据源配置
