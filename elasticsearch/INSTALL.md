# Elasticsearch 安装教程

## 前置条件

- JDK 11+（新版 ES 自带 bundled JDK）
- Linux/macOS/Windows
- 至少 4GB RAM

## 安装步骤（Linux）

### 1. 添加仓库并安装（Debian/Ubuntu）

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
sudo apt update && sudo apt install elasticsearch
```

### 2. 配置

编辑 `/etc/elasticsearch/elasticsearch.yml`：

```yaml
cluster.name: my-cluster
node.name: node-1
network.host: 0.0.0.0
discovery.seed_hosts: ["host1", "host2"]
cluster.initial_master_nodes: ["node-1"]
```

### 3. 系统参数调整

```bash
# 设置 vm.max_map_count
sudo sysctl -w vm.max_map_count=262144
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
```

### 4. 启动

```bash
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
```

### 5. 验证

```bash
curl -u elastic https://localhost:9200
```

首次启动时，ES 8.x 会自动生成 elastic 用户的密码并输出到终端，请务必保存。

## 注意事项

- 不要以 root 用户运行 Elasticsearch
- JVM 堆内存建议设为系统内存的 50%，不超过 32GB
- 生产环境至少 3 个 Master 候选节点
- 使用防火墙限制 9200/9300 端口的访问
