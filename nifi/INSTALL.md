# Apache NiFi 安装教程

## 前置条件

- Java 21+（NiFi 2.x）或 Java 8/11（NiFi 1.x）

## 安装步骤

### 1. 下载解压

从 [NiFi 官网](https://nifi.apache.org/download.html) 下载二进制包：

```bash
tar -xzf nifi-2.x.x-bin.tar.gz -C /opt/
cd /opt/nifi-2.x.x
```

### 2. 启动

```bash
# 前台运行
bin/nifi.sh run

# 后台运行
bin/nifi.sh start
```

### 3. 获取登录凭据

NiFi 2.x 首次启动会生成随机用户名和密码，查看日志获取：

```bash
grep -i "Generated Username" logs/nifi-app.log
grep -i "Generated Password" logs/nifi-app.log
```

### 4. 访问 Web UI

`https://localhost:8443/nifi`

## 关键配置（conf/nifi.properties）

```properties
nifi.web.https.port=8443
nifi.sensitive.props.key=your-secret-key
nifi.flow.configuration.file=./conf/flow.json.gz
```

## 注意事项

- NiFi 2.x 默认启用 HTTPS
- 生产环境配置外部 ZooKeeper 实现集群模式
- 定期备份 flow.json.gz 配置文件
