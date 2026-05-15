# ClickHouse 使用教程

## 连接方式

```bash
# 命令行客户端
clickhouse-client -h localhost --port 9000 -u default --password

# HTTP 接口
curl "http://localhost:8123/?query=SELECT%201"
```

## 基本操作

### 创建数据库和表

```sql
CREATE DATABASE IF NOT EXISTS mydb;

CREATE TABLE mydb.metrics (
    ts DateTime,
    metric String,
    value Float64,
    tags Map(String, String)
) ENGINE = MergeTree()
ORDER BY (metric, ts)
PARTITION BY toYYYYMM(ts);
```

### 插入数据

```sql
INSERT INTO mydb.metrics VALUES
    ('2024-01-01 00:00:00', 'cpu_usage', 0.75, {'host': 'server1'}),
    ('2024-01-01 00:01:00', 'cpu_usage', 0.82, {'host': 'server1'}),
    ('2024-01-01 00:00:00', 'mem_usage', 0.60, {'host': 'server2'});
```

### 查询分析

```sql
-- 按指标聚合
SELECT metric, avg(value), max(value)
FROM mydb.metrics
WHERE ts BETWEEN '2024-01-01' AND '2024-01-31'
GROUP BY metric;

-- 查看表大小
SELECT table, formatReadableSize(sum(bytes_on_disk)) AS size
FROM system.parts
WHERE database = 'mydb'
GROUP BY table;
```

## 分布式表

```sql
-- 在每个节点创建本地表
CREATE TABLE mydb.metrics_local (...) ENGINE = ReplicatedMergeTree(...)

-- 创建分布式表作为查询入口
CREATE TABLE mydb.metrics_dist AS mydb.metrics_local
ENGINE = Distributed('my_cluster', 'mydb', 'metrics_local', rand());
```

## 性能调优

- 使用 `ORDER BY` 选择高频查询的过滤字段
- 合理设置分区（按天/月），避免分区过多
- 批量插入（每批 1000+ 行），避免逐行写入
- 使用 `PREWHERE` 代替 `WHERE` 减少数据扫描

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Too many parts | 减少写入频率，增大批量大小，或调整 `max_parts_in_total` |
| 内存不足 | 降低 `max_memory_usage`，优化查询减少 GROUP BY 基数 |
| 副本不同步 | 检查 ZooKeeper 连接，使用 `SYSTEM RESTART REPLICA` |
