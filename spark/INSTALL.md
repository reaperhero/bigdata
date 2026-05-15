# Apache Spark 安装教程

## 前置条件

- Java 8/11/17（设置 `JAVA_HOME`）
- Python 3.x（使用 PySpark 时需要）

## 安装步骤

### 1. 下载

从 [Spark 官网](https://spark.apache.org/downloads.html) 下载预编译包。

### 2. 解压并配置环境变量

```bash
tar -xzf spark-3.5.x-bin-hadoop3.tgz -C /opt/
export SPARK_HOME=/opt/spark-3.5.x-bin-hadoop3
export PATH=$PATH:$SPARK_HOME/bin
```

### 3. 验证

```bash
# Scala Shell
spark-shell

# Python Shell
pyspark

# 进入后测试
spark.range(10).show()
```

### 4. 提交到 YARN

配置 `HADOOP_CONF_DIR` 指向 Hadoop 配置目录：

```bash
export HADOOP_CONF_DIR=/etc/hadoop/conf
spark-submit --master yarn --deploy-mode cluster my-app.jar
```

## 关键配置（conf/spark-defaults.conf）

```properties
spark.master                     yarn
spark.driver.memory              2g
spark.executor.memory            4g
spark.executor.cores             2
spark.executor.instances         4
spark.serializer                 org.apache.spark.serializer.KryoSerializer
```
