# ClickHouse 安装教程

## 前置条件

- Linux 系统（推荐 Ubuntu/Debian 或 CentOS/RHEL）
- 至少 4GB RAM，建议 SSD 磁盘
- root 或 sudo 权限

## 安装步骤（Debian/Ubuntu）

### 1. 添加官方仓库

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg

curl -fsSL https://packages.clickhouse.com/rpm/lts/repodata/repomd.xml.key | sudo gpg --dearmor -o /usr/share/keyrings/clickhouse-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/clickhouse-keyring.gpg] https://packages.clickhouse.com/deb stable main" | sudo tee /etc/apt/sources.list.d/clickhouse.list
```

### 2. 安装

```bash
sudo apt-get update
sudo apt-get install -y clickhouse-server clickhouse-client
```

安装过程中会提示设置默认用户密码。

### 3. 启动服务

```bash
sudo systemctl start clickhouse-server
sudo systemctl enable clickhouse-server
sudo systemctl status clickhouse-server
```

### 4. 验证

```bash
clickhouse-client --password
# 进入后执行
SELECT version();
```

## CentOS/RHEL 安装

```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://packages.clickhouse.com/rpm/clickhouse.repo
sudo yum install -y clickhouse-server clickhouse-client
sudo systemctl start clickhouse-server
```

## 关键配置

配置文件位于 `/etc/clickhouse-server/`：

- `config.xml`：服务端口（默认 HTTP 8123、TCP 9000）、日志路径、数据目录
- `users.xml`：用户权限、密码、配额

生产环境建议：
- 禁用 swap
- 增大 `ulimit` 文件描述符至 262144+
- 修改默认密码并限制网络访问
