# HDFS 组件介绍

## 概述

HDFS（Hadoop Distributed File System）是 Apache Hadoop 的核心子项目，专为在廉价商用硬件上存储超大型文件而设计的分布式文件系统。它采用"一次写入、多次读取"的设计理念，适合批量数据处理场景。

## 核心特性

- **高容错性**：数据默认 3 副本存储，任一节点故障不影响数据可用性
- **流式数据访问**：优化顺序读写性能，适合大文件处理
- **PB 级存储**：支持横向扩展至数千节点
- **NameNode 高可用**：支持 Active/Standby HA 架构

## 系统架构

- **NameNode**：管理文件系统命名空间和元数据（文件目录、块位置）
- **DataNode**：实际存储数据块的工作节点
- **SecondaryNameNode / JournalNode**：辅助 NameNode 进行元数据检查点或 HA 日志同步
