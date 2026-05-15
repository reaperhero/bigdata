# Elasticsearch 组件介绍

## 概述

Elasticsearch 是一个分布式、RESTful 风格的搜索和数据分析引擎，基于 Apache Lucene 构建。它支持近实时的索引和搜索，广泛应用于全文搜索、日志分析、安全分析和实时监控场景。

## 核心特性

- **近实时搜索**：文档索引后约 1 秒即可被搜索到
- **基于倒排索引**：强大的全文检索能力，支持分词、模糊匹配、聚合
- **天生分布式**：数据自动分片（Shard）和副本（Replica），易于横向扩展
- **丰富的 REST API**：所有操作通过 HTTP JSON 接口完成
- **强大的聚合能力**：支持 Metrics、Bucket、Pipeline 等多种聚合类型
- **生态完善**：与 Kibana、Logstash、Beats 组成 Elastic Stack（ELK）

## 系统架构

- **Cluster**：由一个或多个节点组成，共享同一个 cluster.name
- **Node**：集群中的一个 Elasticsearch 实例，可担任 Master、Data、Ingest 等角色
- **Index**：文档的逻辑集合，类似于关系数据库中的"数据库"
- **Shard**：Index 的物理切分，每个 Shard 是一个独立的 Lucene 索引
- **Replica**：Shard 的副本，提供高可用和读取扩展
