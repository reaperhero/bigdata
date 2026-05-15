# Apache Impala 使用教程

## 连接

```bash
impala-shell -i <impalad-host>:21050
```

## 基本操作

```sql
-- 同步元数据（Hive 表变更后执行）
INVALIDATE METADATA;

-- 刷新特定表的数据
REFRESH my_table;

-- 查询
SELECT name, COUNT(*) AS cnt
FROM my_table
WHERE dt = '2024-01-01'
GROUP BY name
ORDER BY cnt DESC
LIMIT 100;

-- 创建 Kudu 表
CREATE TABLE events (
    id BIGINT,
    ts TIMESTAMP,
    payload STRING,
    PRIMARY KEY(id)
) PARTITION BY HASH(id) PARTITIONS 16
STORED AS KUDU;

-- Kudu 表支持 UPDATE/DELETE
UPDATE events SET payload = 'new' WHERE id = 1;
DELETE FROM events WHERE id = 2;
```

## 性能优化

- 使用 **Parquet** 列式存储格式获得最佳查询性能
- 合理设置**分区**减少数据扫描量
- 使用 `EXPLAIN` 查看执行计划：`EXPLAIN SELECT ...`
- 使用 `COMPUTE STATS` 更新统计信息帮助优化器生成更优计划

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| 查询报 Metadata 过期 | 执行 `INVALIDATE METADATA` 或 `REFRESH table` |
| 内存不足 | 增大 Impalad 内存限制或降低查询并发 |
| Kudu 表不可见 | 使用 `--kudu_master_hosts` 配置 Impala 指向 Kudu Master |
