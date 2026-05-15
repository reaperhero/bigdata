# Apache Hive 安装教程

## 前置条件

- Java 8+
- Hadoop 集群已运行（HDFS + YARN）
- MySQL/PostgreSQL（作为 Metastore 数据库）

## 安装步骤

### 1. 下载解压

```bash
wget https://downloads.apache.org/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
tar -xzvf apache-hive-3.1.3-bin.tar.gz -C /opt/
export HIVE_HOME=/opt/apache-hive-3.1.3-bin
export PATH=$PATH:$HIVE_HOME/bin
```

### 2. 配置 Metastore 数据库

创建 MySQL 数据库：

```sql
CREATE DATABASE hive_metastore;
GRANT ALL ON hive_metastore.* TO 'hive'@'%' IDENTIFIED BY 'password';
```

将 MySQL JDBC 驱动放入 `$HIVE_HOME/lib/`。

### 3. 配置 hive-site.xml

```xml
<configuration>
  <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://localhost:3306/hive_metastore</value>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.cj.jdbc.Driver</value>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>hive</value>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>password</value>
  </property>
</configuration>
```

### 4. 初始化元数据库

```bash
schematool -dbType mysql -initSchema
```

### 5. 启动

```bash
# 启动 Metastore
hive --service metastore &

# 启动 HiveServer2
hiveserver2 &
```

### 6. 验证

```bash
beeline -u "jdbc:hive2://localhost:10000" -n hive
```
