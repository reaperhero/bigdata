# Trino 安装教程

## 前置条件

- Java 17+
- Linux/macOS

## 安装方式

### 方式一：Docker（推荐快速体验）

```bash
docker run --name trino -d -p 8080:8080 trinodb/trino
```

### 方式二：手动安装

```bash
wget https://repo1.maven.org/maven2/io/trino/trino-server/xxx/trino-server-xxx.tar.gz
tar -xzf trino-server-xxx.tar.gz
cd trino-server-xxx
```

### 配置

**etc/config.properties**：

```properties
coordinator=true
node-scheduler.include-coordinator=true
http-server.http.port=8080
discovery.uri=http://localhost:8080
```

**etc/node.properties**：

```properties
node.environment=production
node.data-dir=/data/trino
```

**etc/catalog/hive.properties**：

```properties
connector.name=hive
hive.metastore.uri=thrift://metastore:9083
```

### 启动

```bash
bin/launcher start
```

### 验证

访问 `http://localhost:8080` 查看 Trino Web UI。
