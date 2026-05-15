# Apache Flink 安装教程

## 前置条件

- Java 8、11 或 17（设置 `JAVA_HOME`）
- Linux/macOS/Windows

## 安装步骤

### 1. 下载

从 [Flink 官网](https://flink.apache.org/downloads.html) 下载最新稳定版二进制包。

### 2. 解压

```bash
tar -xzf flink-*.tgz
cd flink-*
```

### 3. 配置

编辑 `conf/flink-conf.yaml`：

```yaml
jobmanager.rpc.address: localhost
jobmanager.rpc.port: 6123
jobmanager.memory.process.size: 1600m
taskmanager.memory.process.size: 1728m
taskmanager.numberOfTaskSlots: 2
parallelism.default: 2
```

### 4. 启动本地集群

```bash
./bin/start-cluster.sh
```

### 5. 验证

访问 Flink Web UI：`http://localhost:8081`

### 6. 提交示例作业

```bash
./bin/flink run examples/streaming/WordCount.jar
```

## YARN 模式部署

```bash
# Session 模式
./bin/yarn-session.sh -n 4 -jm 1024m -tm 4096m

# Per-Job 模式
./bin/flink run -m yarn-cluster -yn 4 -yjm 1024m -ytm 4096m my-job.jar
```

## 注意事项

- Standalone 模式适合开发测试，生产建议部署到 YARN 或 Kubernetes
- Checkpoint 存储建议使用 HDFS 或 S3，不要使用本地文件系统
- RocksDB 状态后端适合大状态场景
