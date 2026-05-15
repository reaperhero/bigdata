# Presto 组件介绍

## 概述

Presto（PrestoDB）是由 Facebook 开源的分布式 SQL 查询引擎，用于对各种数据源执行交互式分析查询。它不存储数据，而是通过 Connector 架构连接异构数据源（Hive、MySQL、Kafka、Elasticsearch 等），在内存中高速执行查询。

## 核心特性

- **纯内存计算**：查询中间结果不落盘，极快的查询速度
- **Connector 架构**：通过 Catalog 配置不同的数据源连接器
- **标准 ANSI SQL**：完整的 SQL 支持，包含窗口函数、CTE 等
- **联邦查询**：可在单条 SQL 中关联不同数据源的表
- **分离计算与存储**：查询引擎与数据存储解耦

## 系统架构

- **Coordinator**：接收 SQL、解析、优化、调度执行计划
- **Worker**：执行 Task，从数据源获取数据并进行计算
- **Connector**：与外部数据源通信的桥梁（Hive、MySQL、PostgreSQL 等）
- **Catalog**：命名配置，定义如何通过 Connector 访问特定数据源
