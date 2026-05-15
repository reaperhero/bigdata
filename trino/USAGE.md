# Trino 使用教程

## CLI 连接

```bash
# 下载 CLI
wget https://repo1.maven.org/maven2/io/trino/trino-cli/xxx/trino-cli-xxx-executable.jar
chmod +x trino-cli-xxx-executable.jar

# 连接
./trino-cli --server localhost:8080 --catalog hive --schema default
```

## 基本查询

```sql
-- 查看所有 Catalog
SHOW CATALOGS;

-- 查看 Schema
SHOW SCHEMAS FROM hive;

-- 查询
SELECT * FROM hive.default.my_table LIMIT 10;

-- 跨数据源联邦查询
SELECT u.name, o.total
FROM mysql.app.users u
JOIN hive.dw.orders o ON u.id = o.user_id
WHERE o.dt = '2024-01-01';
```

## 监控

- Web UI（`http://coordinator:8080`）：查看查询状态、执行计划、资源消耗
- 对接 BI 工具：通过 JDBC 连接 Tableau、Superset 等

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| 查询超时 | 调整 `query.max-execution-time` 参数 |
| 内存不足 | 增加 Worker 数量或增大 JVM 堆 |
| Catalog 连接失败 | 检查 `etc/catalog/` 下的配置文件 |
