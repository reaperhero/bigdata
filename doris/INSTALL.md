# Apache Doris 安装教程

## 前置条件

- Linux 64 位系统（CentOS 7+ / Ubuntu 18+）
- JDK 8 或 11（FE 依赖）
- GCC 7.3+（BE 编译需要，使用二进制包则不需要）
- 设置 `ulimit` 和关闭 `swap`

## 快速部署（开发测试）

```bash
# 下载快速启动脚本
wget https://dist.apache.org/repos/dist/release/doris/start-doris.sh
chmod 755 start-doris.sh
bash start-doris.sh
```

## 手动部署（生产环境）

### 1. 部署 FE

```bash
tar -xzvf apache-doris-fe-x.x.x.tar.gz
cd apache-doris-fe-x.x.x

# 编辑配置
vim conf/fe.conf
# 关键配置：priority_networks = 192.168.1.0/24

# 启动
bin/start_fe.sh --daemon
```

FE 默认端口：HTTP 8030、RPC 9020、Query 9030、Edit Log 9010

### 2. 部署 BE

```bash
tar -xzvf apache-doris-be-x.x.x.tar.gz
cd apache-doris-be-x.x.x

vim conf/be.conf
# 关键配置：priority_networks = 192.168.1.0/24

bin/start_be.sh --daemon
```

### 3. 注册 BE 到集群

```bash
mysql -h FE_HOST -P 9030 -u root
> ALTER SYSTEM ADD BACKEND "be_host:9050";
```

### 4. 验证

```sql
-- 检查 FE 状态
SELECT host, join_status, alive FROM frontends();
-- 检查 BE 状态
SELECT host, alive FROM backends();
```

## 注意事项

- 生产环境至少部署 3 个 FE（1 Leader + 2 Follower）和 3 个 BE
- 配置多副本防止数据丢失
- 创建非 root 用户用于日常操作
