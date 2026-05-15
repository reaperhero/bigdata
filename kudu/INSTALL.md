# Apache Kudu 安装教程

## 前置条件

- **NTP 时间同步**：Kudu 对时钟偏差极其敏感
- Linux 64 位系统
- 推荐 SSD 磁盘

## 安装步骤

### 通过包管理器（Cloudera 仓库）

```bash
# CentOS/RHEL
sudo yum install kudu kudu-master kudu-tserver kudu-client
```

### 二进制包安装

```bash
tar -xzvf kudu-*.tar.gz -C /opt/
```

## 配置

### Master 配置（master.gflagfile）

```
--fs_wal_dir=/data/kudu/master/wal
--fs_data_dirs=/data/kudu/master/data
--master_addresses=master1:7051,master2:7051,master3:7051
```

### TServer 配置（tserver.gflagfile）

```
--fs_wal_dir=/data/kudu/tserver/wal
--fs_data_dirs=/data/kudu/tserver/data
--tserver_master_addrs=master1:7051,master2:7051,master3:7051
```

### 启动

```bash
# 启动 Master
kudu-master --flagfile=/etc/kudu/conf/master.gflagfile &

# 启动 TServer
kudu-tserver --flagfile=/etc/kudu/conf/tserver.gflagfile &
```

### 验证

- Master Web UI：`http://<master>:8050`
- TServer Web UI：`http://<tserver>:8051`

## 注意事项

- 生产环境部署 3 个 Master 实现 HA
- 确保 NTP 时间偏差 < 500ms，否则 Raft 选举会失败
- 建议使用专用系统用户运行 Kudu
