# Apache Hive 使用教程

## 连接

```bash
beeline -u "jdbc:hive2://localhost:10000" -n hive
```

## DDL 操作

```sql
-- 创建数据库
CREATE DATABASE IF NOT EXISTS mydb;
USE mydb;

-- 创建表
CREATE TABLE employees (
    id INT,
    name STRING,
    salary FLOAT,
    department STRING
) STORED AS PARQUET;

-- 创建分区表
CREATE TABLE logs (
    ip STRING,
    url STRING,
    status INT
) PARTITIONED BY (dt STRING)
STORED AS ORC;

-- 查看表结构
DESCRIBE FORMATTED employees;
```

## DML 操作

```sql
-- 从本地加载数据
LOAD DATA LOCAL INPATH '/path/to/data.csv' INTO TABLE employees;

-- 插入数据
INSERT INTO employees VALUES (1, 'Alice', 50000, 'Engineering');

-- 从查询结果创建表
CREATE TABLE high_salary AS
SELECT * FROM employees WHERE salary > 40000;

-- 分区数据加载
ALTER TABLE logs ADD PARTITION (dt='2024-01-01');
INSERT INTO logs PARTITION (dt='2024-01-01') VALUES ('1.1.1.1', '/index', 200);
```

## 查询

```sql
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
ORDER BY avg_salary DESC;
```

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Metastore 连接失败 | 检查 MySQL 服务和 hive-site.xml 中的连接配置 |
| 查询非常慢 | 使用 Tez 引擎：`SET hive.execution.engine=tez;` |
| ORC/Parquet 格式报错 | 确认 Hive 版本与存储格式兼容 |
