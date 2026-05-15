# Azkaban 安装教程

## 前置条件

- JDK 8 或 11（设置好 `JAVA_HOME`）
- MySQL 5.7+（创建专用库 `azkaban` 并授权）
- Git（用于源码克隆）
- Gradle Wrapper 已随仓库提供

## 安装步骤（Multi-Executor 生产模式）

### 1. 克隆源码

```bash
git clone https://github.com/azkaban/azkaban.git
cd azkaban
git checkout azkaban-3.67.0
```

### 2. 构建发行包

```bash
./gradlew clean build installDist
```

完成后在 `azkaban-dist/` 目录下会生成 `azkaban-solo-server`、`azkaban-web-server`、`azkaban-exec-server` 三个子模块。

### 3. 初始化数据库

```bash
mysql -u root -p < azkaban-db/src/main/resources/sql/create_mysql_tables.sql
```

### 4. 配置 Web Server

编辑 `azkaban-web-server/conf/azkaban.properties`：

```properties
mysql.host=127.0.0.1
mysql.port=3306
mysql.database=azkaban
mysql.user=azkaban
mysql.password=YourPassword
server.port=8081
```

### 5. 配置 Executor Server

编辑 `azkaban-exec-server/conf/azkaban.properties`（与 Web Server 共用同一套 DB 配置）：

```properties
executor.pool.size=20
executor.max.concurrency=100
executor.sla.email=admin@example.com
```

### 6. 启动服务

```bash
# 启动 Web Server
cd azkaban-web-server && bin/start-web.sh

# 启动 Executor（可在多台机器上执行）
cd azkaban-exec-server && bin/start-exec.sh
```

### 7. 验证安装

- 访问 `http://<host>:8081`，默认登录 `azkaban / azkaban`
- 检查 `logs/webserver.log` 和 `logs/executor.log` 是否有错误

## 常见注意事项

- 防火墙需放通 8081（Web）和 Executor 端口（默认 12321）
- 生产环境务必启用 NTP 时间同步
- 建议开启 LDAP/AD 认证，禁用默认 admin 账户
- 定期备份 MySQL 中的 azkaban 数据库
