# Apache Flink 使用教程

## 提交作业

```bash
# 提交 jar 包
./bin/flink run -c com.example.MyJob my-job.jar

# 带参数
./bin/flink run -c com.example.MyJob -p 4 my-job.jar --input /data/input --output /data/output
```

## Flink SQL 示例

```sql
-- 创建 Kafka Source 表
CREATE TABLE page_views (
    user_id BIGINT,
    page_url STRING,
    view_time TIMESTAMP(3),
    WATERMARK FOR view_time AS view_time - INTERVAL '5' SECOND
) WITH (
    'connector' = 'kafka',
    'topic' = 'page_views',
    'properties.bootstrap.servers' = 'localhost:9092',
    'format' = 'json'
);

-- 窗口聚合
SELECT window_start, COUNT(*) AS pv
FROM TABLE(TUMBLE(TABLE page_views, DESCRIPTOR(view_time), INTERVAL '1' MINUTE))
GROUP BY window_start;
```

## 作业管理

```bash
# 查看运行中的作业
./bin/flink list

# 取消作业
./bin/flink cancel <job-id>

# 从 Savepoint 恢复
./bin/flink run -s hdfs:///savepoints/savepoint-xxx my-job.jar
```

## 监控

- Web UI（默认 8081）查看 Job 拓扑、Task 状态、Backpressure
- 对接 Prometheus + Grafana 监控 Checkpoint 耗时、吞吐量等指标

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Checkpoint 超时 | 增大 `execution.checkpointing.timeout`，检查状态大小 |
| Backpressure 严重 | 增加并行度，优化算子逻辑，检查数据倾斜 |
| TaskManager OOM | 增大 `taskmanager.memory.process.size` |
