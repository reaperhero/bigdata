# Kerberos 使用教程

## 基础操作

### 获取票据

```bash
# 交互式（输入密码）
kinit username@EXAMPLE.COM

# 使用 keytab（无密码，适合服务和脚本）
kinit -kt /etc/security/keytabs/hdfs.keytab hdfs/node1@EXAMPLE.COM
```

### 查看票据

```bash
klist
```

### 销毁票据

```bash
kdestroy
```

## Principal 管理

```bash
# 进入管理工具
kadmin -p root/admin

# 创建用户
kadmin: addprinc testuser@EXAMPLE.COM

# 创建服务 principal
kadmin: addprinc -randkey hive/hiveserver@EXAMPLE.COM

# 导出 keytab
kadmin: ktadd -k /tmp/hive.keytab hive/hiveserver@EXAMPLE.COM

# 查看 principal 列表
kadmin: listprincs

# 修改密码
kadmin: cpw testuser@EXAMPLE.COM
```

## 与大数据组件集成

### HDFS

在 `core-site.xml` 中配置：

```xml
<property>
  <name>hadoop.security.authentication</name>
  <value>kerberos</value>
</property>
```

在 `hdfs-site.xml` 中为 NameNode/DataNode 配置 principal 和 keytab。

### Hive

在 `hive-site.xml` 中配置 HiveServer2 的 principal：

```xml
<property>
  <name>hive.server2.authentication</name>
  <value>KERBEROS</value>
</property>
```

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Clock skew too great | 所有节点启用 NTP 同步，时间偏差不超过 5 分钟 |
| kinit: Cannot find KDC | 检查 `/etc/krb5.conf` 中 KDC 地址和 DNS 配置 |
| Keytab 认证失败 | 确认 keytab 中的 principal 与配置一致，用 `klist -kt` 检查 |
