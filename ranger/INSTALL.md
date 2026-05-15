# Apache Ranger 安装教程

## 前置条件

- Java 8+
- MySQL/PostgreSQL（存储策略和审计数据）
- Solr（审计日志索引，可选）

## 安装步骤

### 1. 安装 Ranger Admin

```bash
tar -xzvf ranger-2.x.x-admin.tar.gz
cd ranger-2.x.x-admin
```

编辑 `install.properties`：

```properties
DB_FLAVOR=MYSQL
SQL_CONNECTOR_JAR=/path/to/mysql-connector-java.jar
db_host=localhost
db_name=ranger
db_user=ranger
db_password=password
rangerAdmin_password=admin123
```

运行安装：

```bash
./setup.sh
./eza_server.sh start
```

### 2. 安装 Plugin（以 Hive 为例）

```bash
tar -xzvf ranger-2.x.x-hive-plugin.tar.gz
cd ranger-2.x.x-hive-plugin
```

编辑 `install.properties`：

```properties
POLICY_MGR_URL=http://ranger-admin:6080
REPOSITORY_NAME=hive_repo
COMPONENT_INSTALL_DIR_NAME=/opt/hive
```

```bash
./enable-hive-plugin.sh
# 重启 HiveServer2
```

### 3. 验证

访问 Ranger Admin：`http://<host>:6080`，默认账号 `admin/admin`。
