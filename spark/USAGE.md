# Apache Spark 使用教程

## 提交作业

```bash
# Scala/Java
spark-submit --class org.example.MyApp --master yarn --deploy-mode cluster my-app.jar

# PySpark
spark-submit --master yarn my_script.py
```

## PySpark 示例

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("example").getOrCreate()

# 读取数据
df = spark.read.parquet("hdfs:///data/events/")

# 转换
result = df.filter(df.status == 200) \
    .groupBy("page") \
    .count() \
    .orderBy("count", ascending=False)

result.show()

# 写出
result.write.mode("overwrite").parquet("hdfs:///output/page_counts/")
```

## Spark SQL

```python
df.createOrReplaceTempView("events")
spark.sql("SELECT page, COUNT(*) as cnt FROM events WHERE status=200 GROUP BY page ORDER BY cnt DESC").show()
```

## 监控

- Spark UI（作业运行期间访问 Driver 的 4040 端口）
- History Server：`sbin/start-history-server.sh`

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| OOM | 增大 `spark.executor.memory` 或减少数据分区大小 |
| 数据倾斜 | 使用 `repartition()` 或 `salting` 技术 |
| Shuffle 慢 | 增加 `spark.sql.shuffle.partitions` |
