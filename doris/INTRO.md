# Apache Doris 组件介绍

## 概述

Apache Doris 是一个现代化的 MPP（大规模并行处理）分析型数据库，最初由百度开发，现为 Apache 顶级项目。它以亚秒级查询响应著称，兼容 MySQL 协议，使用标准 SQL，适合实时数据分析场景。

## 核心特性

- **MySQL 协议兼容**：可直接使用 MySQL 客户端和 JDBC/ODBC 连接，无需额外学习成本
- **向量化执行引擎**：利用 SIMD 指令加速 CPU 计算效率
- **多种数据模型**：支持明细模型（Duplicate Key）、聚合模型（Aggregate Key）、主键模型（Unique Key）
- **极简运维**：仅有 FE（Frontend）和 BE（Backend）两类进程，无额外依赖
- **物化视图**：自动选择最优的物化视图加速查询
- **支持多种数据导入方式**：Stream Load、Broker Load、Routine Load、Insert Into

## 系统架构

- **FE（Frontend）**：负责用户连接、SQL 解析、执行计划生成、元数据管理
- **BE（Backend）**：负责数据存储和查询执行
- FE 支持 Leader/Follower/Observer 角色实现高可用
- BE 支持多副本，数据自动均衡
