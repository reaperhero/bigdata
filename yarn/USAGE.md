# Apache YARN 使用教程

## 常用命令

```bash
# 查看集群状态
yarn node -list

# 查看运行中的应用
yarn application -list

# 查看应用详情
yarn application -status <application_id>

# 杀死应用
yarn application -kill <application_id>

# 查看应用日志
yarn logs -applicationId <application_id>

# 查看队列信息
yarn queue -status default
```

## 提交作业到 YARN

```bash
# MapReduce
hadoop jar hadoop-mapreduce-examples.jar wordcount /input /output

# Spark
spark-submit --master yarn --deploy-mode cluster my-app.jar

# Flink
flink run -m yarn-cluster -yn 4 my-job.jar
```

## ResourceManager Web UI

访问 `http://rm-host:8088` 可查看：
- 集群总资源和已用资源
- 运行中/已完成/失败的应用列表
- 各队列的资源使用情况
- 各节点的健康状态

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Container 被 kill（OOM）| 增大 `yarn.nodemanager.resource.memory-mb` 或作业内存配置 |
| 应用卡在 ACCEPTED | 检查队列资源是否充足，是否有其他应用占满资源 |
| NodeManager 不健康 | 检查 `yarn.nodemanager.local-dirs` 磁盘空间 |
