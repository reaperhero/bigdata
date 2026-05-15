# Azkaban 组件介绍

## 概述

Azkaban 是 LinkedIn 开源的批量工作流调度平台，专为 Hadoop 集群上的大规模离线任务设计。它基于有向无环图（DAG）表达作业依赖，提供容错重试、并发控制以及 SLA 告警，帮助数据团队实现可视化、可审计、可靠的任务编排。

## 核心特性

- **友好的 Web UI 与 REST API**：通过浏览器直接创建、编辑、调度 Flow，也可通过 API 完全自动化
- **项目工作空间隔离**：每个 Project 拥有独立的权限和配额，适合多团队协作
- **作业压缩包上传**：支持将 `.flow` 与 `.job` 文件打包为 ZIP 统一管理
- **SLA 超时告警**：可为 Flow 配置执行时长阈值，超时自动发送邮件通知
- **插件生态**：提供 Executor Plugin 与自定义 Job 插件，轻松扩展 Spark、Hive、Shell 等执行器

## 系统架构

- **Web Server**：负责 UI、调度请求、项目/Flow/Job 元数据的 CRUD
- **Executor Server**：实际执行 Job 的工作进程，可横向扩展为多实例
- **MySQL（或 MariaDB）**：统一存储元数据、日志、调度状态
- **通信机制**：Web Server 与 Executor 采用长轮询 + 心跳保持连接，确保任务可靠下发

## 关键概念

| 概念 | 说明 |
|------|------|
| Project | 逻辑工作空间，包含多个 Flow |
| Flow | 由若干 Job 通过 DAG 编排的业务逻辑单元 |
| Job | 最小执行单元，支持 command、java、hadoop、spark、hive 等类型 |
| Executor | 实际运行 Job 的进程，支持多线程与资源限制 |
| SLA | 监控 Flow 执行耗时，超时触发告警并可自动重试 |

## 部署模式

| 模式 | 适用场景 | 说明 |
|------|----------|------|
| Solo Server | 开发测试 | Web 和 Executor 运行在同一进程中 |
| Two-Server | 小型生产 | Web 和 Executor 分离部署 |
| Multi-Executor | 生产环境 | 多个 Executor 节点，使用外部 MySQL |
