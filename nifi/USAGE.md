# Apache NiFi 使用教程

## 创建第一个数据流

### 场景：文件从 A 目录移动到 B 目录

1. **添加 GetFile Processor**：从画布顶部拖拽 Processor 图标 → 搜索 `GetFile` → 配置 Input Directory
2. **添加 PutFile Processor**：同样拖拽 → 搜索 `PutFile` → 配置 Directory
3. **连接**：鼠标悬停在 GetFile 上出现箭头 → 拖到 PutFile → 选择 `success` 关系
4. **启动**：右键每个 Processor → Start

### 场景：Kafka → HDFS

1. 添加 `ConsumeKafka` Processor，配置 bootstrap servers 和 topic
2. 添加 `PutHDFS` Processor，配置 HDFS 路径
3. 连接两个 Processor
4. 启动数据流

## 常用 Processor

| Processor | 用途 |
|-----------|------|
| GetFile / PutFile | 文件系统读写 |
| ConsumeKafka / PublishKafka | Kafka 消费/生产 |
| PutHDFS / GetHDFS | HDFS 读写 |
| ExecuteSQL / PutDatabaseRecord | 数据库操作 |
| InvokeHTTP | HTTP 请求 |
| JoltTransformJSON | JSON 数据转换 |

## 监控

- 每个 Processor 上实时显示输入/输出数据量和队列深度
- 右键 FlowFile → View Data Provenance 查看数据流转历史
- 使用 Bulletin Board 查看告警和错误

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| 队列堆积 | 检查下游 Processor 是否正常，调整并发数 |
| FlowFile 丢失 | 检查 Connection 的 expiration 和 back pressure 配置 |
| 内存不足 | 调整 `bootstrap.conf` 中的 JVM 参数 |
