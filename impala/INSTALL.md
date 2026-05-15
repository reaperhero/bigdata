# Apache Impala 安装教程

## 前置条件

- Hadoop 集群（HDFS + YARN）已运行
- Hive Metastore 已部署
- 推荐通过 Cloudera Manager 或 CDP 安装（生产环境）

## 手动安装（通过包管理器）

### CentOS/RHEL

```bash
# 添加 Cloudera 仓库
sudo yum install impala impala-server impala-state-store impala-catalog impala-shell
```

### 配置

Impala 需要读取 Hadoop 的 XML 配置文件（core-site.xml、hdfs-site.xml、hive-site.xml）。

### 启动各组件

```bash
# 在主节点
sudo service impala-state-store start
sudo service impala-catalog start

# 在每个数据节点
sudo service impala-server start
```

### 验证

```bash
impala-shell -i <impalad-host>
> SHOW DATABASES;
```

## 注意事项

- 从源码编译安装流程复杂，生产环境推荐使用 Cloudera 发行版
- Impala 需要足够的内存，建议每个 Impalad 至少分配 4GB+
- 新建 Hive 表后需在 Impala 执行 `INVALIDATE METADATA` 同步
