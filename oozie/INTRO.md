# Apache Oozie 组件介绍

## 概述

Apache Oozie 是一个用于管理 Hadoop 作业的工作流调度系统。它通过 XML 定义作业的 DAG（有向无环图），支持将 MapReduce、Hive、Pig、Sqoop、Spark、Shell 等多种作业编排成复杂的数据管道。

## 核心特性

- **DAG 工作流**：通过 XML 定义作业间的依赖关系
- **定时调度（Coordinator）**：基于时间或数据可用性触发工作流
- **Bundle**：将多个 Coordinator 和 Workflow 打包为一个管理单元
- **丰富的 Action 节点**：内置 MR、Hive、Pig、Spark、Shell、SSH 等
- **Web Console**：提供可视化监控界面

## 核心概念

| 概念 | 说明 |
|------|------|
| Workflow | 由多个 Action 通过 DAG 组成的作业序列 |
| Coordinator | 基于时间或数据触发的调度器 |
| Bundle | 多个 Coordinator 的打包管理单元 |
| Action | 实际执行的作业（MR、Hive、Shell 等）|
