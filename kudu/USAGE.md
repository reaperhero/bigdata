# Apache Kudu 使用教程

## 通过 Impala 操作 Kudu

### 创建表

```sql
CREATE TABLE events (
    id BIGINT,
    ts TIMESTAMP,
    payload STRING,
    PRIMARY KEY(id)
) PARTITION BY HASH(id) PARTITIONS 16
STORED AS KUDU;
```

### DML 操作

```sql
INSERT INTO events VALUES (1, NOW(), 'test data');
UPDATE events SET payload = 'updated' WHERE id = 1;
DELETE FROM events WHERE id = 1;
UPSERT INTO events VALUES (2, NOW(), 'upsert data');
```

### 映射已有 Kudu 表

```sql
CREATE EXTERNAL TABLE my_mapping
STORED AS KUDU
TBLPROPERTIES ('kudu.table_name' = 'existing_kudu_table');
```

## 通过 Spark 读写 Kudu

```python
df = spark.read.format('kudu') \
    .option('kudu.master', 'master:7051') \
    .option('kudu.table', 'events') \
    .load()

df.show()

df.write.format('kudu') \
    .option('kudu.master', 'master:7051') \
    .option('kudu.table', 'events') \
    .mode('append') \
    .save()
```

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Clock synchronization error | 检查 NTP 同步状态：`ntpq -p` |
| Tablet not running | 查看 TServer 日志，检查磁盘空间和 Raft 状态 |
| Impala 看不到 Kudu 表 | 配置 Impala 的 `--kudu_master_hosts` 参数 |
