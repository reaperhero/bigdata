# Apache Hive 组件介绍

## 概述

Apache Hive 是建立在 Hadoop 上的数据仓库基础构架，提供类 SQL 查询语言（HiveQL）来查询和管理存储在 HDFS 上的大规模数据。Hive 将 SQL 转换为 MapReduce/Tez/Spark 任务执行，适合离线批量数据分析。

## 核心特性

- **类 SQL 接口（HiveQL）**：语法接近标准 SQL，降低使用门槛
- **多种存储格式**：支持 Parquet、ORC、TextFile、Avro 等
- **元数据中心化管理**：通过 Metastore 统一管理表定义和分区信息
- **可插拔执行引擎**：支持 MapReduce、Tez、Spark 作为执行后端
- **分区和分桶**：优化大表查询性能

## 系统架构

- **HiveServer2**：对外提供 JDBC/ODBC 服务，接收客户端 SQL 请求
- **Metastore**：存储表、列、分区等元数据，底层使用 MySQL/PostgreSQL
- **执行引擎**：将 HiveQL 编译为 MR/Tez/Spark 任务提交到 YARN
- **HDFS**：实际存储表数据
