# Apache Kafka 使用教程

## 命令行操作

### Topic 管理

```bash
# 创建
bin/kafka-topics.sh --create --topic my-topic --partitions 3 --replication-factor 2 --bootstrap-server localhost:9092

# 查看详情
bin/kafka-topics.sh --describe --topic my-topic --bootstrap-server localhost:9092

# 删除
bin/kafka-topics.sh --delete --topic my-topic --bootstrap-server localhost:9092
```

### 生产消息

```bash
bin/kafka-console-producer.sh --topic my-topic --bootstrap-server localhost:9092
> Hello Kafka
> This is a test message
```

### 消费消息

```bash
# 从头消费
bin/kafka-console-consumer.sh --topic my-topic --from-beginning --bootstrap-server localhost:9092

# 指定消费组
bin/kafka-console-consumer.sh --topic my-topic --group my-group --bootstrap-server localhost:9092
```

### 消费组管理

```bash
# 查看消费组
bin/kafka-consumer-groups.sh --list --bootstrap-server localhost:9092

# 查看消费进度（lag）
bin/kafka-consumer-groups.sh --describe --group my-group --bootstrap-server localhost:9092
```

## Java Producer 示例

```java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

KafkaProducer<String, String> producer = new KafkaProducer<>(props);
producer.send(new ProducerRecord<>("my-topic", "key", "value"));
producer.close();
```

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Consumer Lag 持续增长 | 增加 Consumer 实例数（不超过 Partition 数）|
| Broker 磁盘满 | 调整 `log.retention.hours` 或增加磁盘 |
| 消息丢失 | 设置 `acks=all` + `min.insync.replicas=2` |
