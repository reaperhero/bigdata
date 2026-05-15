# HDFS 安装教程

## 前置条件

- Java 8/11（设置 `JAVA_HOME`）
- 各节点间免密 SSH
- 创建专用 hadoop 用户

## 安装步骤

### 1. 下载解压 Hadoop

```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.x/hadoop-3.3.x.tar.gz
tar -xzvf hadoop-3.3.x.tar.gz -C /opt/
```

### 2. 配置环境变量

```bash
export HADOOP_HOME=/opt/hadoop-3.3.x
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

### 3. 配置文件

**etc/hadoop/core-site.xml**：

```xml
<property>
  <name>fs.defaultFS</name>
  <value>hdfs://namenode:9000</value>
</property>
```

**etc/hadoop/hdfs-site.xml**：

```xml
<property>
  <name>dfs.replication</name>
  <value>3</value>
</property>
<property>
  <name>dfs.namenode.name.dir</name>
  <value>/data/hadoop/namenode</value>
</property>
<property>
  <name>dfs.datanode.data.dir</name>
  <value>/data/hadoop/datanode</value>
</property>
```

### 4. 格式化 NameNode

```bash
hdfs namenode -format
```

### 5. 启动

```bash
start-dfs.sh
```

### 6. 验证

- `jps` 查看 NameNode/DataNode 进程
- Web UI：`http://namenode:9870`
