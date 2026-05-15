# Apache HBase 安装教程

## 前置条件

- Java 8+（设置 `JAVA_HOME`）
- Hadoop HDFS 集群已运行（分布式模式）
- ZooKeeper 集群已运行（或使用 HBase 内置 ZK）
- 各节点间免密 SSH

## 安装步骤

### 1. 下载解压

```bash
wget https://downloads.apache.org/hbase/2.5.x/hbase-2.5.x-bin.tar.gz
tar -xzvf hbase-2.5.x-bin.tar.gz -C /opt/
```

### 2. 配置

**conf/hbase-env.sh**：

```bash
export JAVA_HOME=/usr/lib/jvm/java-8
export HBASE_MANAGES_ZK=false  # 使用外部 ZK
```

**conf/hbase-site.xml**：

```xml
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://namenode:8020/hbase</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zk1,zk2,zk3</value>
  </property>
</configuration>
```

**conf/regionservers**：

```
node1
node2
node3
```

### 3. 启动

```bash
bin/start-hbase.sh
```

### 4. 验证

- HBase Shell：`bin/hbase shell`
- Master Web UI：`http://<master>:16010`
