# Presto 安装教程

## 前置条件

- Java 17+
- Python 2.7 或 3.x（launcher 依赖）
- 至少 8GB RAM

## 安装步骤

### 1. 下载解压

```bash
wget https://repo1.maven.org/maven2/com/facebook/presto/presto-server/0.296/presto-server-0.296.tar.gz
tar -xvzf presto-server-0.296.tar.gz
cd presto-server-0.296
mkdir etc
```

### 2. 配置

**etc/node.properties**：

```properties
node.environment=production
node.id=ffffffff-ffff-ffff-ffff-ffffffffffff
node.data-dir=/data/presto
```

**etc/config.properties**（Coordinator）：

```properties
coordinator=true
node-scheduler.include-coordinator=false
http-server.http.port=8080
discovery.uri=http://coordinator:8080
```

**etc/jvm.config**：

```
-server
-Xmx8G
-XX:+UseG1GC
```

### 3. 配置 Catalog

**etc/catalog/hive.properties**：

```properties
connector.name=hive
hive.metastore.uri=thrift://metastore:9083
```

**etc/catalog/mysql.properties**：

```properties
connector.name=mysql
connection-url=jdbc:mysql://localhost:3306
connection-user=root
connection-password=secret
```

### 4. 启动

```bash
bin/launcher start
```

### 5. 验证

访问 `http://coordinator:8080` 查看 Web UI。
