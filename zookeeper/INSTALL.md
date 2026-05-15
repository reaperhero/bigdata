# Apache ZooKeeper 安装教程

## 前置条件

- Java 8+

## 安装步骤

### 1. 下载解压

```bash
wget https://downloads.apache.org/zookeeper/zookeeper-3.9.x/apache-zookeeper-3.9.x-bin.tar.gz
tar -xzf apache-zookeeper-3.9.x-bin.tar.gz -C /opt/
cd /opt/apache-zookeeper-3.9.x-bin
```

### 2. 配置

```bash
cp conf/zoo_sample.cfg conf/zoo.cfg
```

编辑 `conf/zoo.cfg`：

```properties
tickTime=2000
dataDir=/data/zookeeper
clientPort=2181
initLimit=5
syncLimit=2

# 集群配置（3 节点示例）
server.1=zk1:2888:3888
server.2=zk2:2888:3888
server.3=zk3:2888:3888
```

### 3. 创建 myid 文件

在每个节点的 `dataDir` 目录下创建 `myid` 文件，内容为该节点的序号：

```bash
# 在 zk1 上
echo "1" > /data/zookeeper/myid

# 在 zk2 上
echo "2" > /data/zookeeper/myid

# 在 zk3 上
echo "3" > /data/zookeeper/myid
```

### 4. 启动

在每个节点上执行：

```bash
bin/zkServer.sh start
```

### 5. 验证

```bash
bin/zkServer.sh status
# 应显示 leader 或 follower
```
