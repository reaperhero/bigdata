# Apache Spark 组件介绍

## 概述

Apache Spark 是用于大规模数据处理的统一分析引擎。它以内存计算为核心，比传统 MapReduce 快数十到数百倍，支持批处理、流处理、机器学习（MLlib）和图计算（GraphX）。

## 核心特性

- **内存计算**：基于 RDD/DataFrame 的弹性分布式数据集，减少磁盘 IO
- **多语言 API**：支持 Scala、Java、Python、R、SQL
- **批流统一**：Structured Streaming 实现统一的批流处理模型
- **丰富的库**：Spark SQL、MLlib、GraphX、Structured Streaming
- **多种部署模式**：Standalone、YARN、Mesos、Kubernetes

## 系统架构

- **Driver**：运行 main() 函数，创建 SparkContext，将作业分解为 Task
- **Executor**：运行在 Worker 节点上，执行具体的 Task 并缓存数据
- **Cluster Manager**：负责资源分配（YARN/K8s/Standalone）
