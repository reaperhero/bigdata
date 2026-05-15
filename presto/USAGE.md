# Presto 使用教程

## CLI 连接

```bash
# 下载 CLI
wget https://repo1.maven.org/maven2/com/facebook/presto/presto-cli/0.296/presto-cli-0.296-executable.jar
mv presto-cli-0.296-executable.jar presto-cli
chmod +x presto-cli

# 连接
./presto-cli --server coordinator:8080 --catalog hive --schema default
```

## 基本查询

```sql
-- 查看 Catalog
SHOW CATALOGS;

-- 查看数据库
SHOW SCHEMAS FROM hive;

-- 查看表
SHOW TABLES FROM hive.default;

-- 查询
SELECT * FROM hive.default.my_table LIMIT 10;

-- 跨源联邦查询
SELECT a.name, b.order_count
FROM mysql.mydb.users a
JOIN hive.default.orders b ON a.id = b.user_id;
```

## 性能优化

- 使用 Parquet/ORC 格式存储数据
- 合理设置分区减少数据扫描
- 使用 `EXPLAIN` 分析执行计划
- 控制查询并发数，避免内存争抢

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Query exceeded memory limit | 增大 Worker 内存或优化查询减少数据量 |
| Connector 配置错误 | 检查 `etc/catalog/` 下的配置文件语法 |
| Worker 节点未注册 | 确认 `discovery.uri` 配置正确且网络通畅 |
