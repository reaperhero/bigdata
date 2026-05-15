# Kerberos 安装教程

## 前置条件

- **NTP 时间同步**：Kerberos 对时间偏差极其敏感，所有节点必须同步
- **DNS**：正确配置正向和反向 DNS 解析
- **FQDN**：所有机器设置完整的主机名

## 安装 KDC 服务端

### Debian/Ubuntu

```bash
sudo apt update
sudo apt install krb5-kdc krb5-admin-server
```

### CentOS/RHEL

```bash
sudo yum install krb5-server krb5-libs krb5-workstation
```

## 配置

### /etc/krb5.conf

```ini
[libdefaults]
    default_realm = EXAMPLE.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false

[realms]
    EXAMPLE.COM = {
        kdc = kdc.example.com
        admin_server = kdc.example.com
    }

[domain_realm]
    .example.com = EXAMPLE.COM
    example.com = EXAMPLE.COM
```

### 初始化 KDC 数据库

```bash
sudo kdb5_util create -s
# 输入并牢记 Master Password
```

### 配置 ACL

编辑 `/var/kerberos/krb5kdc/kadm5.acl`：

```
*/admin@EXAMPLE.COM    *
```

### 启动服务

```bash
sudo systemctl enable krb5-kdc krb5-admin-server
sudo systemctl start krb5-kdc krb5-admin-server
```

## 创建 Principal

```bash
sudo kadmin.local
kadmin.local: addprinc root/admin
kadmin.local: addprinc hdfs/node1@EXAMPLE.COM
kadmin.local: ktadd -k /etc/security/keytabs/hdfs.keytab hdfs/node1@EXAMPLE.COM
```

## 客户端安装

```bash
# Debian/Ubuntu
sudo apt install krb5-user

# CentOS/RHEL
sudo yum install krb5-workstation
```

配置同样的 `/etc/krb5.conf`。
