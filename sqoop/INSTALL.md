# Apache Sqoop 安装教程

## 前置条件

- Java 8+
- Hadoop 集群已运行
- 目标数据库的 JDBC 驱动

## 安装步骤

### 1. 下载解压

```bash
wget https://archive.apache.org/dist/sqoop/1.4.7/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
tar -xzvf sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz -C /opt/
export SQOOP_HOME=/opt/sqoop-1.4.7.bin__hadoop-2.6.0
export PATH=$PATH:$SQOOP_HOME/bin
```

### 2. 添加 JDBC 驱动

```bash
cp mysql-connector-java-8.0.x.jar $SQOOP_HOME/lib/
```

### 3. 配置

编辑 `conf/sqoop-env.sh`：

```bash
export HADOOP_COMMON_HOME=/opt/hadoop
export HADOOP_MAPRED_HOME=/opt/hadoop
export HIVE_HOME=/opt/hive
```

### 4. 验证

```bash
sqoop version
sqoop list-databases --connect jdbc:mysql://localhost:3306/ --username root -P
```
