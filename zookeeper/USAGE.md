# Apache ZooKeeper 使用教程

## CLI 操作

### 连接

```bash
bin/zkCli.sh -server localhost:2181
```

### 基本命令

```bash
# 查看根节点
ls /

# 创建节点
create /myapp "config_data"

# 创建临时节点
create -e /myapp/lock "locked"

# 创建顺序节点
create -s /myapp/task- "task_data"

# 查看节点数据
get /myapp

# 修改节点数据
set /myapp "new_config"

# 查看节点状态
stat /myapp

# 删除节点
delete /myapp
deleteall /myapp   # 递归删除
```

## 典型应用场景

### 配置管理

各服务启动时从 ZooKeeper 读取配置，Watch 监听配置变更实现动态更新。

### 分布式锁

创建临时顺序节点，序号最小的获得锁，其他节点 Watch 前一个节点。

### Leader 选举

多个候选节点创建临时节点，序号最小的成为 Leader，Leader 退出后自动触发重新选举。

## 为大数据组件配置 ZooKeeper

大多数组件的配置格式为逗号分隔的地址列表：

```
zk1:2181,zk2:2181,zk3:2181
```

例如 Kafka 的 `zookeeper.connect`、HBase 的 `hbase.zookeeper.quorum` 等。

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| 无法选举 Leader | 确保奇数个节点且多数节点在线 |
| 连接超时 | 检查 clientPort 和防火墙 |
| 数据目录满 | 配置自动清理：`autopurge.snapRetainCount=3` |
