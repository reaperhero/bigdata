# 大数据集群 Kerberos 开启状态检测

## 1. 目标

本文用于判断 HDFS、YARN 等大数据组件是否已经开启 Kerberos，以及 Kerberos 是否真正生效。

判断不能只看“进程参数里有 `krb5.conf`”，更可靠的判断要同时看：

- 配置是否启用 Kerberos。
- principal 和 keytab 是否配置完整。
- 服务日志是否使用 keytab 登录成功。
- 客户端命令是否需要 Kerberos ticket 才能访问。

## 2. 总体判断标准

| 判断维度 | 说明 | 是否可靠 |
| --- | --- | --- |
| 配置文件 | `hadoop.security.authentication=kerberos`，并且各服务有 principal/keytab | 高 |
| 服务日志 | 出现 `Login successful for user ... using keytab` | 高 |
| 客户端验证 | 未 kinit 访问失败，kinit 后访问成功 | 最高 |
| 进程参数 | 进程包含 `-Djava.security.krb5.conf=...` | 中 |
| keytab 文件存在 | 只能说明文件存在，不能说明已启用 | 低 |

最可靠结论应同时满足：

1. `core-site.xml` 中认证方式是 `kerberos`。
2. 组件配置中存在服务 principal 和 keytab。
3. 服务日志显示 keytab 登录成功。
4. 客户端命令在 kinit 后可以正常访问。

## 3. 通用检测命令

### 3.1 检查 Hadoop 全局认证配置

```bash
grep -nE "hadoop.security.authentication|hadoop.security.authorization" \
  /opt/dtstack/HDFS/hdfs_pkg/etc/hadoop/core-site.xml \
  /opt/dtstack/Yarn/yarn_pkg/etc/hadoop/core-site.xml
```

开启 Kerberos 时应看到类似：

```text
hadoop.security.authentication=kerberos
hadoop.security.authorization=true
```

如果是：

```text
hadoop.security.authentication=simple
```

则说明 Hadoop 认证仍是普通模式。

### 3.2 检查 krb5.conf

```bash
ls -l /etc/krb5.conf
grep -nE "default_realm|kdc|admin_server" /etc/krb5.conf
```

如果集群使用自定义路径，例如：

```bash
/opt/dtstack/Kerberos/kerberos_pkg/conf/krb5.conf
```

也要检查该路径。

### 3.3 检查 keytab 内容

```bash
klist -kte /data/kerberos/keytab/hdfs.keytab
klist -kte /data/kerberos/keytab/yarn.keytab
```

应能看到对应服务 principal，例如：

```text
hdfs/node226@DTSTACK.COM
yarn/node226@DTSTACK.COM
HTTP/node226@DTSTACK.COM
```

注意：keytab 中存在 principal 不代表服务已经启用 Kerberos，只能说明认证材料存在。

## 4. HDFS 检测

### 4.1 检查 HDFS 配置

```bash
grep -nE "kerberos|principal|keytab|authentication|authorization" \
  /opt/dtstack/HDFS/hdfs_pkg/etc/hadoop/core-site.xml \
  /opt/dtstack/HDFS/hdfs_pkg/etc/hadoop/hdfs-site.xml
```

重点配置：

```text
hadoop.security.authentication=kerberos
hadoop.security.authorization=true
dfs.namenode.kerberos.principal=hdfs/_HOST@REALM
dfs.datanode.kerberos.principal=hdfs/_HOST@REALM
dfs.web.authentication.kerberos.principal=HTTP/_HOST@REALM
dfs.namenode.keytab.file=/data/kerberos/keytab/hdfs.keytab
dfs.datanode.keytab.file=/data/kerberos/keytab/hdfs.keytab
```

### 4.2 检查 HDFS 进程参数

```bash
ps -ef | grep -E "NameNode|DataNode|JournalNode|DFSZKFailoverController" | grep -v grep
```

如果看到：

```text
-Djava.security.krb5.conf=/opt/dtstack/Kerberos/kerberos_pkg/conf/krb5.conf
```

说明进程加载了 Kerberos 客户端配置。

### 4.3 检查 HDFS 登录日志

```bash
grep -nE "Login successful|LoginException|GSSException|keytab|principal|Kerberos" \
  /opt/dtstack/HDFS/hdfs_namenode/logs/*.log \
  /opt/dtstack/HDFS/hdfs_datanode/logs/*.log \
  /opt/dtstack/HDFS/hdfs_journalnode/logs/*.log 2>/dev/null
```

正常日志示例：

```text
Login successful for user hdfs/node226@DTSTACK.COM using keytab file hdfs.keytab
```

异常日志示例：

```text
LoginException
GSSException
Server not found in Kerberos database
No valid credentials provided
```

### 4.4 使用客户端验证 HDFS

```bash
sudo -u hdfs kdestroy 2>/dev/null || true
sudo -u hdfs hdfs dfs -ls /
```

如果未认证时失败，再执行：

```bash
sudo -u hdfs kinit -kt /data/kerberos/keytab/hdfs.keytab hdfs/$(hostname -f)@DTSTACK.COM
sudo -u hdfs klist
sudo -u hdfs hdfs dfs -ls /
```

kinit 后访问成功，说明 HDFS Kerberos 链路可用。

如果 principal 使用短主机名，则把 `$(hostname -f)` 改成 `$(hostname)`。

## 5. YARN 检测

### 5.1 检查 YARN 配置

```bash
grep -nE "kerberos|principal|keytab|authentication|authorization" \
  /opt/dtstack/Yarn/yarn_pkg/etc/hadoop/core-site.xml \
  /opt/dtstack/Yarn/yarn_pkg/etc/hadoop/yarn-site.xml
```

重点配置：

```text
hadoop.security.authentication=kerberos
hadoop.security.authorization=true
yarn.resourcemanager.principal=yarn/_HOST@REALM
yarn.resourcemanager.keytab=/data/kerberos/keytab/yarn.keytab
yarn.nodemanager.principal=yarn/_HOST@REALM
yarn.nodemanager.keytab=/data/kerberos/keytab/yarn.keytab
```

### 5.2 检查 ResourceManager 是否启用 Kerberos

配置检查：

```bash
grep -nE "yarn.resourcemanager.principal|yarn.resourcemanager.keytab|hadoop.security.authentication" \
  /opt/dtstack/Yarn/yarn_pkg/etc/hadoop/core-site.xml \
  /opt/dtstack/Yarn/yarn_pkg/etc/hadoop/yarn-site.xml
```

进程检查：

```bash
ps -ef | grep ResourceManager | grep -v grep
```

日志检查：

```bash
grep -nE "Login successful|LoginException|GSSException|keytab|principal|Kerberos" \
  /opt/dtstack/Yarn/yarn_resourcemanager/logs/yarn-yarn-resourcemanager-*.out
```

正常日志示例：

```text
Login successful for user yarn/node226@DTSTACK.COM using keytab file yarn.keytab
```

HA 场景还应确认 Active RM 的 `8031` 端口可用：

```bash
ss -lntp | grep 8031
```

`8031` 是 NodeManager 向 ResourceManager 注册使用的 ResourceTracker 端口。

### 5.3 检查 NodeManager 是否启用 Kerberos

配置检查：

```bash
grep -nE "yarn.nodemanager.principal|yarn.nodemanager.keytab|hadoop.security.authentication" \
  /opt/dtstack/Yarn/yarn_pkg/etc/hadoop/core-site.xml \
  /opt/dtstack/Yarn/yarn_pkg/etc/hadoop/yarn-site.xml
```

进程检查：

```bash
ps -ef | grep NodeManager | grep -v grep
```

日志检查：

```bash
grep -nE "Login successful|LoginException|GSSException|keytab|principal|Kerberos|registerNodeManager" \
  /opt/dtstack/Yarn/yarn_nodemanager/logs/yarn-yarn-nodemanager-*.out
```

正常情况应同时满足：

- NodeManager 进程存在。
- 日志中没有持续的 `LoginException` / `GSSException`。
- NodeManager 能注册到 Active ResourceManager。

如果看到：

```text
Connection refused ... registerNodeManager ... 8031
```

优先检查 Active ResourceManager 的 `8031` 是否在当时已经监听。

### 5.4 使用客户端验证 YARN

```bash
sudo -u yarn kdestroy 2>/dev/null || true
sudo -u yarn yarn node -list
```

如果未认证时失败，再执行：

```bash
sudo -u yarn kinit -kt /data/kerberos/keytab/yarn.keytab yarn/$(hostname -f)@DTSTACK.COM
sudo -u yarn klist
sudo -u yarn yarn node -list
sudo -u yarn yarn application -list
```

kinit 后命令成功，说明 YARN Kerberos 客户端链路可用。

## 6. ZooKeeper 检测

### 6.1 检查 JAAS 配置

```bash
grep -nE "Krb5LoginModule|principal|keyTab|useKeyTab" \
  /opt/dtstack/Zookeeper/zookeeper*/conf/jaas.conf 2>/dev/null
```

重点检查：

```text
principal="zookeeper/<hostname>@REALM"
keyTab="/data/kerberos/keytab/zookeeper.keytab"
useKeyTab=true
```

### 6.2 检查 ZooKeeper 日志

```bash
grep -nE "SASL|LoginException|No password provided|GSSException|Kerberos|authenticated" \
  /opt/dtstack/Zookeeper/zookeeper*/logs/* 2>/dev/null
```

如果出现：

```text
No password provided
```

常见原因是 keytab 不可读、principal 和 keytab 不匹配，或 JAAS principal 写成了其他节点。

## 7. Hive 检测

```bash
grep -nE "kerberos|principal|keytab|authentication" \
  /opt/dtstack/Hive/hive_pkg/conf/hive-site.xml 2>/dev/null
```

重点配置：

```text
hive.server2.authentication=KERBEROS
hive.server2.authentication.kerberos.principal=hive/_HOST@REALM
hive.server2.authentication.kerberos.keytab=/data/kerberos/keytab/hive.keytab
```

客户端验证：

```bash
kinit -kt /data/kerberos/keytab/hive.keytab hive/$(hostname -f)@DTSTACK.COM
beeline -u "jdbc:hive2://<hiveserver2_host>:10000/default;principal=hive/<hiveserver2_host>@DTSTACK.COM"
```

## 8. Kafka 检测

Kafka 是否开启 Kerberos 通常看 SASL/GSSAPI 配置。

```bash
grep -RsnE "SASL|GSSAPI|Kerberos|principal|keyTab|security.inter.broker.protocol|sasl.kerberos" \
  /opt/dtstack/Kafka 2>/dev/null
```

典型配置：

```text
security.inter.broker.protocol=SASL_PLAINTEXT
sasl.mechanism.inter.broker.protocol=GSSAPI
sasl.kerberos.service.name=kafka
```

并检查 JAAS 中是否包含：

```text
com.sun.security.auth.module.Krb5LoginModule required
useKeyTab=true
keyTab="/data/kerberos/keytab/kafka.keytab"
principal="kafka/<hostname>@REALM";
```

## 9. 常见误判

| 现象 | 正确理解 |
| --- | --- |
| 进程里有 `krb5.conf` | 只能说明加载了 Kerberos 配置文件，不代表已认证成功 |
| keytab 文件存在 | 只能说明认证材料存在，不代表服务启用了 Kerberos |
| `klist -kte` 能看到 principal | 只能说明 keytab 可解析，不代表服务端配置正确 |
| standby RM 没监听 `8031` | HA standby 状态下可能只监听管理/Web 端口，要看 active RM |
| 日志里有历史 `Connection refused` | 需要看时间线，确认当时 RM 端口是否已监听 |
| `_HOST` 配置存在 | 还要确认反向解析后的 hostname 和 keytab principal 匹配 |

## 10. 快速结论模板

排查完成后建议按下面格式记录：

| 项目 | 结论 |
| --- | --- |
| `hadoop.security.authentication` | `kerberos` / `simple` |
| 服务 principal/keytab | 已配置 / 未配置 / 配置不完整 |
| keytab 内容 | principal 匹配 / 不匹配 |
| 服务日志 | 登录成功 / 登录失败 / 未看到登录日志 |
| 客户端命令 | kinit 后成功 / 仍失败 |
| 最终判断 | 已开启并生效 / 已配置但未生效 / 未开启 |

