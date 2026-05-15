# Apache Atlas 安装教程

## 前置条件

- **Java**：OpenJDK 8 或 11
- **Maven**：3.5+（从源码构建时需要）
- **Python**：Python 3（管理脚本依赖）
- **底层依赖**：Apache HBase（图数据存储）、Apache Solr 或 Elasticsearch（搜索索引）、ZooKeeper、Kafka

## 安装方式一：源码编译

1. **下载源码**

   从 [Apache Atlas 官网](https://atlas.apache.org/) 下载最新源码包，或直接 clone 仓库：

   ```bash
   git clone https://github.com/apache/atlas.git
   cd atlas
   git checkout release-2.3.0   # 选择合适的版本
   ```

2. **编译打包**

   ```bash
   export MAVEN_OPTS="-Xms2g -Xmx2g"
   mvn clean package -DskipTests
   ```

   编译完成后，发布包位于 `distro/target/` 目录。

3. **解压安装**

   ```bash
   tar -xzvf distro/target/apache-atlas-*-server.tar.gz -C /opt/
   cd /opt/apache-atlas-*
   ```

## 安装方式二：Docker 快速部署

社区提供 Docker Compose 配置，可一键拉起 Atlas 及其全部依赖（HBase、Solr、Kafka、ZooKeeper）：

```bash
docker-compose up -d
```

适用于本地开发与测试环境。

## 配置

进入 `conf/` 目录，编辑 `atlas-application.properties`：

```properties
# 图数据库后端
atlas.graph.storage.backend=hbase
atlas.graph.storage.hostname=localhost

# 搜索索引
atlas.graph.index.search.backend=solr
atlas.graph.index.search.solr.zookeeper-url=localhost:2181/solr

# Kafka 通知
atlas.notification.embedded=false
atlas.kafka.zookeeper.connect=localhost:2181
atlas.kafka.bootstrap.servers=localhost:9092
```

## 启动服务

**开发模式**（内置 HBase 和 Solr）：

```bash
export MANAGE_LOCAL_HBASE=true
export MANAGE_LOCAL_SOLR=true
python3 bin/atlas_start.py
```

**生产模式**（外部 HBase 和 Solr）：

```bash
# 确保外部 HBase、Solr、Kafka 已启动
bin/atlas_start.py -setup   # 首次运行需要初始化
```

## 验证安装

- 访问 Web UI：`http://<host>:21000`
- 默认账号密码：`admin / admin`
- 查看日志：`logs/application.log`

## 常见注意事项

- 首次启动时需要运行 `-setup` 参数或在配置中设置 `atlas.server.run.setup.on.start=true`
- 生产环境务必修改默认密码，并启用 Kerberos 或 LDAP 认证
- HBase 和 Solr 的连接配置必须与实际部署一致，否则 Atlas 无法启动
- 防火墙需放通 21000（Web UI）端口
