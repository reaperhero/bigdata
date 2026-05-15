# Apache Sqoop 组件介绍

## 概述

Apache Sqoop（SQL-to-Hadoop）是一个专为 Hadoop 体系与关系型数据库之间高效批量传输数据而设计的工具。它底层将数据传输转化为 MapReduce 任务执行，支持高并行度的数据导入导出。

> **注意**：Apache Sqoop 已于 2021 年 6 月退役（moved to Apache Attic），不再活跃维护。新项目建议使用 Spark JDBC、DataX 或 NiFi 替代。

## 核心特性

- **双向传输**：支持 RDBMS → HDFS（Import）和 HDFS → RDBMS（Export）
- **并行 MapReduce**：数据按主键范围切分，多个 Mapper 并行传输
- **直接导入 Hive/HBase**：使用 `--hive-import` 或 `--hbase-table` 参数
- **增量导入**：支持基于时间戳或自增列的增量数据同步
