# 大数据集群 Kerberos 开启流程

## 1. 目标

本文说明在已部署的大数据集群中开启 Kerberos 的标准操作流程，适用于 HDFS、YARN、Hive、HBase、Kafka、Ranger 等组件逐步纳入 Kerberos 认证的场景。

开启 Kerberos 的核心目标是：

- 为集群服务端进程启用基于 principal/keytab 的身份认证。
- 为客户端访问 HDFS、YARN、Hive、HBase、Kafka 等服务提供统一认证入口。
- 避免未认证用户直接访问核心数据服务。

## 2. 前置条件

| 检查项 | 要求 | 原因 |
| --- | --- | --- |
| KDC 服务 | Kerberos KDC 已部署并正常运行 | 所有 principal 和 ticket 都依赖 KDC |
| 时间同步 | 所有节点时间误差建议小于 5 分钟 | Kerberos ticket 对时间敏感，时间偏差会导致认证失败 |
| 主机名解析 | 所有节点 hostname、FQDN、IP 解析一致 | principal 通常包含 hostname，解析错误会导致认证失败 |
| 服务用户 | hdfs、yarn、hive、hbase、kafka 等系统用户存在 | 服务进程需要用对应用户读取 keytab 并启动 |
| keytab 目录 | 约定目录存在，例如 `/data/kerberos/keytab` | 统一存放服务 keytab，便于配置和权限管理 |
| krb5.conf | 所有节点能获取一致的 Kerberos 客户端配置 | 客户端需要知道 realm、KDC 地址等信息 |
| 集群状态 | 当前集群服务处于健康状态 | 避免把已有服务异常误判为 Kerberos 开启失败 |

## 3. 开启前备份

| 内容 | 建议备份方式 | 用途 |
| --- | --- | --- |
| KDC 数据 | 备份 KDC 数据库和配置 | KDC 异常时恢复 principal 信息 |
| 服务配置 | 备份各组件当前配置文件 | 开启失败时对比或回滚 |
| keytab 文件 | 备份已生成 keytab | 防止误删或覆盖 |
| EasyManager 配置 | 记录本次修改的 Kerberos 开关和配置项 | 便于排查和回退 |

常见配置路径示例：

```bash
/etc/krb5.conf
/data/kerberos/keytab
/opt/dtstack/HDFS/hdfs_pkg/etc/hadoop
/opt/dtstack/Yarn/yarn_pkg/etc/hadoop
```

## 4. 操作流程

### 4.1 部署并验证 Kerberos 基础服务

1. 部署 Kerberos 服务端组件。
2. 确认 KDC、kadmin 服务正常。
3. 确认 realm 配置正确，例如 `DTSTACK.COM`。
4. 在任意节点执行基础认证验证：

```bash
kinit admin/admin
klist
```

如果 `kinit` 成功并能看到 ticket，说明 KDC 基础能力可用。

### 4.2 生成服务 principal 和 keytab

为每个需要开启 Kerberos 的服务生成 principal 和 keytab。

常见 principal 格式：

```text
hdfs/<hostname>@REALM
yarn/<hostname>@REALM
hive/<hostname>@REALM
hbase/<hostname>@REALM
kafka/<hostname>@REALM
zookeeper/<hostname>@REALM
HTTP/<hostname>@REALM
```

注意事项：

- `<hostname>` 必须和服务配置中使用的 hostname 一致。
- HA 服务需要给每个服务节点分别生成 principal。
- Web UI 或 SPNEGO 场景通常需要 `HTTP/<hostname>@REALM`。
- keytab 权限要保证服务运行用户可读。

权限示例：

```bash
chown hdfs:hadoop /data/kerberos/keytab/hdfs.keytab
chmod 400 /data/kerberos/keytab/hdfs.keytab
```

### 4.3 分发 krb5.conf 和 keytab

将 Kerberos 客户端配置和 keytab 分发到所有相关节点。

建议检查：

```bash
ls -l /etc/krb5.conf
ls -l /data/kerberos/keytab
klist -kte /data/kerberos/keytab/hdfs.keytab
```

`klist -kte` 应能看到对应 principal，例如：

```text
hdfs/node001@DTSTACK.COM
HTTP/node001@DTSTACK.COM
```

### 4.4 开启组件 Kerberos 配置

在 EasyManager 或组件配置中开启 Kerberos 开关，并确认相关配置项已正确渲染。

常见配置项包括：

| 配置项 | 示例 | 说明 |
| --- | --- | --- |
| realm | `DTSTACK.COM` | Kerberos 默认 realm |
| kdc 地址 | `kdc01.example.com` | KDC 服务地址 |
| keytab 路径 | `/data/kerberos/keytab/hdfs.keytab` | 服务认证 keytab |
| principal | `hdfs/_HOST@DTSTACK.COM` 或 `hdfs/node001@DTSTACK.COM` | 服务 principal |
| krb5.conf | `/etc/krb5.conf` | Kerberos 客户端配置 |

Hadoop 相关常见配置包括：

```xml
hadoop.security.authentication=kerberos
hadoop.security.authorization=true
dfs.namenode.kerberos.principal=hdfs/_HOST@REALM
dfs.datanode.kerberos.principal=hdfs/_HOST@REALM
dfs.web.authentication.kerberos.principal=HTTP/_HOST@REALM
yarn.resourcemanager.principal=yarn/_HOST@REALM
yarn.nodemanager.principal=yarn/_HOST@REALM
```

如果模板不使用 `_HOST`，则必须确保每个节点渲染出的 hostname 与本机 keytab principal 一致。

### 4.5 按依赖顺序刷新配置并重启服务

Kerberos 开启后，通常需要重启相关服务才能生效。

建议顺序：

| 顺序 | 服务 | 原因 |
| --- | --- | --- |
| 1 | Kerberos/KDC | 认证中心必须先可用 |
| 2 | ZooKeeper | 多数 HA 组件依赖 ZooKeeper |
| 3 | HDFS JournalNode | NameNode HA 依赖 JournalNode |
| 4 | HDFS ZKFC | ZKFC 依赖 ZooKeeper 和 HDFS HA 配置 |
| 5 | HDFS NameNode | NameNode 依赖 JournalNode 和 ZKFC 相关配置 |
| 6 | HDFS DataNode | DataNode 依赖 NameNode |
| 7 | YARN ResourceManager | YARN 核心服务 |
| 8 | YARN NodeManager | NodeManager 依赖 ResourceManager |
| 9 | Hive/HBase/Kafka/Spark 等上层服务 | 上层服务通常依赖 HDFS/YARN/ZooKeeper |

如果由平台执行批量启动，应确认实际启动顺序符合组件依赖关系。

## 5. 验证步骤

### 5.1 验证 keytab 可用

在对应节点使用服务用户验证：

```bash
sudo -u hdfs kinit -kt /data/kerberos/keytab/hdfs.keytab hdfs/$(hostname -f)@DTSTACK.COM
sudo -u hdfs klist
```

如果 principal 使用短主机名，则使用：

```bash
sudo -u hdfs kinit -kt /data/kerberos/keytab/hdfs.keytab hdfs/$(hostname)@DTSTACK.COM
```

### 5.2 验证 HDFS

```bash
sudo -u hdfs kinit -kt /data/kerberos/keytab/hdfs.keytab hdfs/$(hostname -f)@DTSTACK.COM
hdfs dfs -ls /
hdfs haadmin -getServiceState nn1
hdfs haadmin -getServiceState nn2
```

预期：

- `hdfs dfs -ls /` 能正常返回。
- HA 场景下一个 NameNode 为 `active`，另一个为 `standby`。

### 5.3 验证 YARN

```bash
sudo -u yarn kinit -kt /data/kerberos/keytab/yarn.keytab yarn/$(hostname -f)@DTSTACK.COM
yarn node -list
yarn application -list
```

预期：

- 能正常获取 NodeManager 和 application 列表。

### 5.4 验证 Hive

```bash
kinit -kt /data/kerberos/keytab/hive.keytab hive/$(hostname -f)@DTSTACK.COM
beeline -u "jdbc:hive2://<hiveserver2_host>:10000/default;principal=hive/<hiveserver2_host>@DTSTACK.COM"
```

预期：

- Beeline 能成功连接。
- 能执行简单 SQL。

### 5.5 验证 Kafka

根据 Kafka 是否启用 SASL/GSSAPI，检查客户端 JAAS、krb5.conf 和 principal。

常见检查：

```bash
kinit -kt /data/kerberos/keytab/kafka.keytab kafka/$(hostname -f)@DTSTACK.COM
klist
```

然后使用带 Kerberos 配置的 producer/consumer 客户端进行收发验证。

## 6. 常见问题和处理

| 现象 | 常见原因 | 处理方式 |
| --- | --- | --- |
| `Client not found in Kerberos database` | principal 未创建或名称不一致 | 检查 principal 和 keytab |
| `Preauthentication failed` | keytab 与 KDC 中 principal 密钥不一致 | 重新生成并分发 keytab |
| `Clock skew too great` | 节点时间偏差过大 | 修复 NTP/chrony 时间同步 |
| `No valid credentials provided` | 未 kinit 或 ticket 过期 | 重新 kinit，检查 ticket 生命周期 |
| `Server not found in Kerberos database` | 服务端 principal 与访问 hostname 不一致 | 统一 hostname、FQDN、principal |
| 服务启动失败提示 JAAS `No password provided` | keytab 不可读或 principal 不匹配 | 检查 keytab 权限和 JAAS principal |
| Web UI Kerberos 失败 | 缺少 HTTP principal 或浏览器未配置 SPNEGO | 生成 HTTP keytab 并配置浏览器认证 |

## 7. 回滚方案

如果开启后出现大面积服务不可用，可按以下方式回滚：

1. 停止受影响服务。
2. 关闭组件 Kerberos 开关。
3. 恢复开启前备份的配置文件。
4. 按依赖顺序重启服务。
5. 验证 HDFS/YARN/Hive 等核心链路恢复。

回滚时不建议直接删除 KDC 数据和 keytab，除非确认后续不再使用 Kerberos。

## 8. 验收标准

开启完成后应满足：

- 所有核心服务启动成功。
- HDFS/YARN/Hive/HBase/Kafka 等组件通过 Kerberos 客户端验证。
- 非认证用户无法直接访问受保护服务。
- 服务日志中无持续 Kerberos、SASL、JAAS、GSSAPI 报错。
- 平台上的服务状态、健康检查和监控指标恢复正常。

## 9. 操作记录模板

| 项目 | 内容 |
| --- | --- |
| 集群名称 |  |
| Kerberos Realm |  |
| KDC 节点 |  |
| 开启时间 |  |
| 操作人 |  |
| 涉及组件 |  |
| 备份路径 |  |
| 验证结果 |  |
| 遗留问题 |  |

