# Apache ZooKeeper 组件介绍

## 概述

Apache ZooKeeper 是一个分布式协调服务，为分布式应用提供高可用的配置管理、命名服务、分布式锁和集群管理功能。它是 Hadoop、HBase、Kafka、Flink 等众多大数据组件的核心依赖。

## 核心特性

- **强一致性**：基于 ZAB（ZooKeeper Atomic Broadcast）协议
- **高可用**：集群模式下只要多数节点存活即可提供服务
- **树状命名空间**：类似文件系统的 Znode 层次结构
- **Watcher 机制**：客户端可监听 Znode 变更事件，实现实时通知

## 核心概念

| 概念 | 说明 |
|------|------|
| Znode | ZooKeeper 的数据节点，类似文件系统中的文件/目录 |
| Ensemble | ZooKeeper 集群（通常为奇数个节点：3、5、7）|
| Quorum | 多数派，写操作需要超过半数节点确认 |
| Watch | 监听机制，当 Znode 变更时通知客户端 |
| Ephemeral Node | 临时节点，创建它的客户端断开后自动删除 |
