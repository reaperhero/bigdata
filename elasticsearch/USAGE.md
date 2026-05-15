# Elasticsearch 使用教程

## 索引操作

### 创建索引

```bash
curl -X PUT "localhost:9200/my_index" -H 'Content-Type: application/json' -d '{
  "settings": { "number_of_shards": 3, "number_of_replicas": 1 },
  "mappings": {
    "properties": {
      "title": { "type": "text", "analyzer": "standard" },
      "timestamp": { "type": "date" },
      "value": { "type": "float" }
    }
  }
}'
```

### 索引文档

```bash
curl -X POST "localhost:9200/my_index/_doc/1" -H 'Content-Type: application/json' -d '{
  "title": "Hello Elasticsearch",
  "timestamp": "2024-01-01T00:00:00",
  "value": 42.0
}'
```

### 搜索

```bash
# 全文搜索
curl "localhost:9200/my_index/_search?q=title:hello"

# DSL 查询
curl -X POST "localhost:9200/my_index/_search" -H 'Content-Type: application/json' -d '{
  "query": { "match": { "title": "elasticsearch" } },
  "sort": [{ "timestamp": "desc" }],
  "size": 10
}'
```

### 聚合分析

```bash
curl -X POST "localhost:9200/my_index/_search" -H 'Content-Type: application/json' -d '{
  "size": 0,
  "aggs": {
    "avg_value": { "avg": { "field": "value" } },
    "by_month": {
      "date_histogram": { "field": "timestamp", "calendar_interval": "month" }
    }
  }
}'
```

## 集群管理

```bash
# 查看集群健康
curl "localhost:9200/_cluster/health?pretty"

# 查看节点状态
curl "localhost:9200/_cat/nodes?v"

# 查看索引列表
curl "localhost:9200/_cat/indices?v"
```

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| 集群状态 RED | 有分片未分配，使用 `_cluster/allocation/explain` 排查 |
| 磁盘空间不足 | ES 会自动只读，清理数据后执行 `PUT _all/_settings {"index.blocks.read_only_allow_delete":null}` |
| 查询慢 | 检查 Mapping 是否合理，避免在 text 字段上做聚合 |
