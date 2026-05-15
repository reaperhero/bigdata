# Apache Sqoop 使用教程

## 导入（RDBMS → HDFS）

```bash
sqoop import \
  --connect jdbc:mysql://localhost:3306/mydb \
  --username root --password secret \
  --table users \
  --target-dir /user/hadoop/users \
  --num-mappers 4
```

## 导入到 Hive

```bash
sqoop import \
  --connect jdbc:mysql://localhost:3306/mydb \
  --table users \
  --hive-import \
  --hive-table mydb.users \
  --username root --password secret
```

## 增量导入

```bash
sqoop import \
  --connect jdbc:mysql://localhost:3306/mydb \
  --table orders \
  --incremental lastmodified \
  --check-column update_time \
  --last-value "2024-01-01 00:00:00" \
  --target-dir /user/hadoop/orders
```

## 导出（HDFS → RDBMS）

```bash
sqoop export \
  --connect jdbc:mysql://localhost:3306/mydb \
  --table users_export \
  --export-dir /user/hadoop/users \
  --username root --password secret
```

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| ClassNotFoundException | 检查 JDBC 驱动是否在 `$SQOOP_HOME/lib/` |
| 导入速度慢 | 增加 `--num-mappers`，确保表有合适的 `--split-by` 列 |
| Hive 表乱码 | 检查字符集和 `--hive-delims-replacement` 参数 |
