# HDFS 使用教程

## 常用命令

```bash
# 查看目录
hdfs dfs -ls /

# 创建目录
hdfs dfs -mkdir -p /user/hadoop/data

# 上传文件
hdfs dfs -put local_file.txt /user/hadoop/data/

# 下载文件
hdfs dfs -get /user/hadoop/data/file.txt ./

# 查看文件内容
hdfs dfs -cat /user/hadoop/data/file.txt

# 删除文件
hdfs dfs -rm /user/hadoop/data/file.txt

# 查看磁盘使用
hdfs dfs -du -h /user/
```

## 管理命令

```bash
# 查看集群报告
hdfs dfsadmin -report

# 安全模式
hdfs dfsadmin -safemode get
hdfs dfsadmin -safemode leave

# 均衡数据
hdfs balancer -threshold 5
```

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| NameNode 进入安全模式 | 等待 DataNode 报告足够的块，或手动 `safemode leave` |
| 磁盘使用不均 | 运行 `hdfs balancer` |
| 小文件过多 | 使用 HAR 归档或 CombineFileInputFormat 合并 |
