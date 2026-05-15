# Apache Doris 使用教程

## 连接

```bash
mysql -h FE_HOST -P 9030 -u root
```

## 基本操作

### 创建数据库和表

```sql
CREATE DATABASE demo;
USE demo;

CREATE TABLE user_events (
    event_date DATE,
    user_id BIGINT,
    event_type VARCHAR(50),
    amount DECIMAL(10,2)
) DUPLICATE KEY(event_date, user_id)
DISTRIBUTED BY HASH(user_id) BUCKETS 8
PROPERTIES ("replication_num" = "3");
```

### 数据导入

**Stream Load（HTTP 方式）**：

```bash
curl -u root: -H "label:load_20240101" \
  -H "column_separator:," \
  -T data.csv \
  http://FE_HOST:8030/api/demo/user_events/_stream_load
```

**INSERT INTO**：

```sql
INSERT INTO user_events VALUES
('2024-01-01', 1001, 'click', 0),
('2024-01-01', 1002, 'purchase', 99.99);
```

### 查询分析

```sql
SELECT event_type, COUNT(*), SUM(amount)
FROM user_events
WHERE event_date BETWEEN '2024-01-01' AND '2024-01-31'
GROUP BY event_type
ORDER BY COUNT(*) DESC;
```

## 高级功能

- **物化视图**：`CREATE MATERIALIZED VIEW mv_summary AS SELECT event_type, SUM(amount) FROM user_events GROUP BY event_type;`
- **动态分区**：自动按天/周/月创建和删除分区
- **Routine Load**：从 Kafka 持续消费数据

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| BE 启动失败 | 检查 `ulimit -n` 是否足够大（建议 65536+）|
| 查询超时 | 调整 `query_timeout` 参数，或优化表的分桶策略 |
| 数据不均衡 | 使用 `ADMIN SHOW REPLICA DISTRIBUTION` 查看副本分布 |
