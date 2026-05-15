# OpenLDAP 安装教程

## 安装

### Debian/Ubuntu

```bash
sudo apt update
sudo apt install slapd ldap-utils
```

安装过程中会提示设置管理员密码。

### CentOS/RHEL

```bash
sudo yum install openldap-servers openldap-clients
```

## 初始配置

### 重新配置（Ubuntu）

```bash
sudo dpkg-reconfigure slapd
```

按提示设置：DNS 域名、组织名称、管理员密码、数据库类型。

### 验证

```bash
ldapsearch -x -LLL -H ldap:/// -b "dc=example,dc=com"
```

## 添加基础组织结构

创建 `base.ldif`：

```ldif
dn: ou=users,dc=example,dc=com
objectClass: organizationalUnit
ou: users

dn: ou=groups,dc=example,dc=com
objectClass: organizationalUnit
ou: groups
```

导入：

```bash
ldapadd -x -D "cn=admin,dc=example,dc=com" -W -f base.ldif
```

## 启用 TLS

```bash
# 生成证书
sudo openssl req -new -x509 -nodes -keyout /etc/ldap/slapd-key.pem -out /etc/ldap/slapd-cert.pem -days 365

# 配置 slapd 使用证书（通过 LDIF 修改 cn=config）
```

## 注意事项

- 现代 OpenLDAP 使用 `cn=config` 动态配置，避免直接编辑配置文件
- 用 `ldapmodify` 和 `ldapadd` 操作配置变更
- 使用 `slapcat` 定期备份
