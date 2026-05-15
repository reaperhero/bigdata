# Apache Kafka 安装教程

## 前置条件

- Java 17+（`java -version` 确认）
- Linux/macOS/Windows

## 安装步骤（KRaft 模式，无需 ZooKeeper）

### 1. 下载解压

```bash
wget https://downloads.apache.org/kafka/3.7.0/kafka_2.13-3.7.0.tgz
tar -xzf kafka_2.13-3.7.0.tgz
cd kafka_2.13-3.7.0
```

### 2. 生成集群 ID 并格式化

```bash
KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"
bin/kafka-storage.sh format --standalone -t $KAFKA_CLUSTER_ID -c config/server.properties
```

### 3. 启动

```bash
bin/kafka-server-start.sh config/server.properties
```

### 4. 验证

```bash
# 创建 Topic
bin/kafka-topics.sh --create --topic test --bootstrap-server localhost:9092

# 查看 Topic
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

## Docker 方式

```bash
docker pull apache/kafka:latest
docker run -p 9092:9092 apache/kafka:latest
```

## 关键配置（config/server.properties）

```properties
broker.id=0
listeners=PLAINTEXT://:9092
log.dirs=/data/kafka-logs
num.partitions=3
log.retention.hours=168
```
