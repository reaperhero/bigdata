# DataX 使用教程

## Job 配置文件结构

DataX 的核心是 JSON 配置文件，定义了数据同步的源端（Reader）、目标端（Writer）和运行参数：

```json
{
  "job": {
    "setting": {
      "speed": { "channel": 4 },
      "errorLimit": { "record": 0, "percentage": 0.02 }
    },
    "content": [{
      "reader": {
        "name": "mysqlreader",
        "parameter": {
          "username": "root",
          "password": "password",
          "column": ["id", "name", "age"],
          "connection": [{
            "jdbcUrl": ["jdbc:mysql://127.0.0.1:3306/testdb"],
            "table": ["users"]
          }]
        }
      },
      "writer": {
        "name": "hdfswriter",
        "parameter": {
          "defaultFS": "hdfs://namenode:8020",
          "fileType": "orc",
          "path": "/user/hive/warehouse/users",
          "fileName": "users",
          "column": [
            {"name": "id", "type": "bigint"},
            {"name": "name", "type": "string"},
            {"name": "age", "type": "int"}
          ],
          "writeMode": "append"
        }
      }
    }]
  }
}
```

## 执行同步任务

```bash
python bin/datax.py job/mysql2hdfs.json
```

执行完成后会输出统计信息：同步速率、总记录数、脏数据条数等。

## 常用 Reader/Writer 组合

| 场景 | Reader | Writer |
|------|--------|--------|
| MySQL → HDFS | mysqlreader | hdfswriter |
| MySQL → Hive | mysqlreader | hdfswriter + Hive 外表 |
| HDFS → MySQL | hdfsreader | mysqlwriter |
| Oracle → HBase | oraclereader | hbasewriter |
| MySQL → Elasticsearch | mysqlreader | elasticsearchwriter |

## 性能调优

- **增大 channel 数**：`"speed": {"channel": 8}` 提升并行度
- **限速控制**：`"speed": {"byte": 1048576}` 限制每秒传输字节数，避免对源库压力过大
- **splitPk**：在 Reader 中指定 `splitPk` 字段（通常为主键），使 DataX 按主键范围切分数据实现并行读取

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| ClassNotFoundException | 检查 JDBC 驱动是否放入正确的 plugin/reader/libs 目录 |
| 脏数据超限 | 调整 `errorLimit` 或检查源数据质量 |
| OOM | 增大 JVM 内存：修改 `bin/datax.py` 中的 `-Xmx` 参数 |
| 写入速度慢 | 增大 channel 数，使用批量写入模式 |
